<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8">
  <title>Countdown naar onze hereniging</title>
  <style>
    /* Romantische styling */
    body {
      font-family: 'Georgia', serif;
      background: linear-gradient(to bottom, #ffafbd, #ffc3a0);
      margin: 0;
      padding: 20px;
      text-align: center;
      color: #333;
    }
    .container {
      max-width: 600px;
      margin: 50px auto;
      background: rgba(255, 255, 255, 0.9);
      padding: 30px;
      border-radius: 15px;
      box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
    }
    input, button {
      padding: 10px;
      font-size: 16px;
      margin: 10px 0;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    .countdown {
      font-size: 2.5em;
      margin-top: 20px;
      font-weight: bold;
    }
    .heart {
      color: red;
      font-size: 3em;
      animation: beat 1s infinite;
      vertical-align: middle;
    }
    @keyframes beat {
      0% { transform: scale(1); }
      25% { transform: scale(1.2); }
      50% { transform: scale(1); }
      75% { transform: scale(1.2); }
      100% { transform: scale(1); }
    }
    a {
      word-break: break-all;
      color: #007BFF;
      text-decoration: none;
    }
    label {
      display: block;
      margin-bottom: 5px;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <!-- De container waarin de inhoud verschijnt -->
  <div class="container" id="app"></div>

  <script>
    window.onload = function() {
      console.log("Script loaded.");

      // Functie om queryparameters uit de URL te halen
      function getQueryParams() {
        var params = {};
        var queryString = window.location.search.substring(1);
        var pairs = queryString.split("&");
        for (var i = 0; i < pairs.length; i++) {
          if (pairs[i] === "") continue;
          var pair = pairs[i].split("=");
          params[decodeURIComponent(pair[0])] = decodeURIComponent(pair[1] || "");
        }
        return params;
      }
      
      var app = document.getElementById("app");
      var params = getQueryParams();
      console.log("Parameters:", params);
      
      // Als er zowel 'deadline' als 'title' in de URL staan, tonen we de timer
      if (params.deadline && params.title) {
        var title = params.title;
        var deadline = new Date(params.deadline);
        console.log("Deadline parsed:", deadline);
        
        if (isNaN(deadline.getTime())) {
          app.innerHTML = "<p>Ongeldige datum. Controleer de ingevoerde deadline.</p>";
          return;
        }
        
        // Toon de timer en de deelbare link
        app.innerHTML = '<h1>' + title + ' <span class="heart">&#10084;</span></h1>' +
                        '<div class="countdown" id="countdown">Laden...</div>';
                        
        var currentURL = window.location.href;
        app.innerHTML += '<p>Deel deze timer:</p>' +
                         '<p><input type="text" id="shareLink" value="' + currentURL + '" readonly style="width:100%; padding:10px; font-size:16px; text-align:center;"></p>' +
                         '<p><button id="copyButton">Kopieer link</button></p>';
                         
        document.getElementById("copyButton").addEventListener("click", function() {
          var shareLink = document.getElementById("shareLink");
          shareLink.select();
          shareLink.setSelectionRange(0, 99999); // Voor mobiele apparaten
          try {
            if (document.execCommand("copy")) {
              alert("Link gekopieerd: " + shareLink.value);
            } else {
              alert("Kopiëren is niet gelukt. Probeer handmatig te kopiëren.");
            }
          } catch (err) {
            alert("Kopiëren wordt niet ondersteund in deze browser.");
          }
        });
        
        // Start de countdown
        var interval;
        function updateCountdown() {
          var now = new Date();
          var diff = deadline - now;
          if (diff <= 0) {
            document.getElementById("countdown").innerHTML = "Eindelijk samen! &#129327;";
            clearInterval(interval);
            return;
          }
          var days = Math.floor(diff / (1000 * 60 * 60 * 24));
          var hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
          var minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
          var seconds = Math.floor((diff % (1000 * 60)) / 1000);
          document.getElementById("countdown").innerHTML = days + "d " + hours + "u " + minutes + "m " + seconds + "s";
        }
        updateCountdown();
        interval = setInterval(updateCountdown, 1000);
        
      } else {
        // Anders tonen we het formulier om een nieuwe timer in te stellen
        app.innerHTML = '<h1>Countdown naar onze hereniging</h1>' +
                        '<p>Stel in wanneer we weer samen zijn</p>' +
                        '<form id="timerForm">' +
                          '<div>' +
                            '<label for="title">Liefdesboodschap:</label>' +
                            '<input type="text" id="title" name="title" placeholder="Bijv. \'Binnenkort weer samen!\'" required>' +
                          '</div>' +
                          '<div>' +
                            '<label for="deadline">Datum en tijd van hereniging:</label>' +
                            '<input type="datetime-local" id="deadline" name="deadline" required>' +
                          '</div>' +
                          '<button type="submit">Maak mijn romantische timer</button>' +
                        '</form>';
                        
        document.getElementById("timerForm").addEventListener("submit", function(e) {
          e.preventDefault();
          var title = document.getElementById("title").value;
          var deadline = document.getElementById("deadline").value;
          if (title && deadline) {
            var encodedTitle = encodeURIComponent(title);
            var encodedDeadline = encodeURIComponent(deadline);
            var url = window.location.origin + window.location.pathname + "?title=" + encodedTitle + "&deadline=" + encodedDeadline;
            app.innerHTML = "<p>Jouw romantische timer is aangemaakt! Deel deze link met je geliefde:</p>" +
                            '<p><a href="' + url + '">' + url + "</a></p>" +
                            '<p><button id="backButton">Nieuwe timer maken</button></p>';
            document.getElementById("backButton").addEventListener("click", function() {
              window.location.href = window.location.pathname;
            });
          }
        });
      }
    };
  </script>
</body>
</html>
