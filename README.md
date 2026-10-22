<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Roblox Badge Test</title>
  <style>
    body {
      margin: 0;
      padding: 24px;
      background: #0e0e0e;
      color: white;
      font-family: -apple-system, BlinkMacSystemFont, sans-serif;
    }
.card {
  max-width: 500px;
  margin: 30px auto;
  padding: 24px;
  background: #1b1b1b;
  border: 1px solid #333;
  border-radius: 20px;
}
h2 {
  margin-top: 0;
}
button {
  width: 100%;
  padding: 14px;
  margin: 8px 0;
  border: 0;
  border-radius: 12px;
  background: white;
  color: black;
  font-size: 16px;
  font-weight: 600;
}
#result {
  margin-top: 20px;
  padding: 14px;
  border-radius: 12px;
  background: #111;
  overflow-wrap: anywhere;
  white-space: pre-wrap;
  line-height: 1.6;
}
.note {
  color: #aaa;
  font-size: 14px;
  line-height: 1.5;
}
  </style>
</head>
<body>
  <div class="card">
    <h2>Badge Ownership Test</h2>
<p class="note">
  Tests whether your browser can retrieve the award date
  for one Adopt Me badge directly from Roblox.
</p>
<button onclick="testOwnership()">
  Test Badge Ownership
</button>
<div id="result">Waiting for test...</div>
  </div>
  <script>
    async function testOwnership() {
      const result = document.getElementById("result");
      result.textContent = "Contacting Roblox...";
      const url =
        "https://badges.roblox.com/v1/users/5584737019/" +
        "badges/awarded-dates?badgeIds=2124439922";
      try {
        const response = await fetch(url, {
          method: "GET",
          headers: {
            "Accept": "application/json"
          }
        });
        const text = await response.text();
        let data;
        try {
          data = JSON.parse(text);
        } catch {
          result.textContent =
            "Roblox responded, but the response wasn't JSON.\n\n" +
            "HTTP status: " + response.status + "\n\n" +
            text.slice(0, 1000);
          return;
        }
        if (!response.ok) {
          result.textContent =
            "Roblox returned an HTTP error.\n\n" +
            "HTTP status: " + response.status + "\n\n" +
            JSON.stringify(data, null, 2);
          return;
        }
        if (!Array.isArray(data.data)) {
          result.textContent =
            "Unexpected response:\n\n" +
            JSON.stringify(data, null, 2);
          return;
        }
        const badge = data.data.find(
          item => String(item.badgeId) === "2124439922"
        );
        if (badge && badge.awardedDate) {
          result.textContent =
            "SUCCESS: Roblox returned an award date!\n\n" +
            "Badge ID: " + badge.badgeId + "\n" +
            "Award date: " + badge.awardedDate;
        } else {
          result.textContent =
            "REQUEST SUCCEEDED, BUT NO AWARD DATE WAS RETURNED.\n\n" +
            "This alone does not prove the badge is unowned.\n\n" +
            JSON.stringify(data, null, 2);
        }
      } catch (error) {
        result.textContent =
          "BROWSER REQUEST FAILED.\n\n" +
          error.name + ": " + error.message + "\n\n" +
          "The browser may be blocking the request because of " +
          "cross-origin (CORS) restrictions. This does not " +
          "necessarily mean Roblox rejected the endpoint.";
      }
    }
  </script>
</body>
</html>
