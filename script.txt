const timeZoneGroups = {
  Asia: [
    { label: "🇯🇵 Tokyo (JST)", value: "Asia/Tokyo" },
    { label: "🇹🇱 Dili (TLT)", value: "Asia/Dili" },
    { label: "🇮🇩 Jakarta (WIB)", value: "Asia/Jakarta" },
    { label: "🇸🇬 Singapore (SGT)", value: "Asia/Singapore" },
    { label: "🇦🇪 Dubai (GST)", value: "Asia/Dubai" },
    { label: "🇰🇷 Seoul (KST)", value: "Asia/Seoul" },
    { label: "🇹🇭 Bangkok (ICT)", value: "Asia/Bangkok" },
    { label: "🇮🇳 Mumbai (IST)", value: "Asia/Kolkata" },
    { label: "🇮🇱 Tel Aviv (IST)", value: "Asia/Jerusalem" }
  ],
  Europe: [
    { label: "🇬🇧 London (GMT)", value: "Europe/London" },
    { label: "🇵🇹 Lisbon (WET)", value: "Europe/Lisbon" },
    { label: "🇩🇪 Berlin (CET)", value: "Europe/Berlin" },
    { label: "🇫🇷 Paris (CET)", value: "Europe/Paris" }
  ],
  Americas: [
    { label: "🇺🇸 New York (EST)", value: "America/New_York" },
    { label: "🇧🇷 São Paulo (BRT)", value: "America/Sao_Paulo" },
    { label: "🇺🇸 Los Angeles (PST)", value: "America/Los_Angeles" },
    { label: "🇲🇽 Mexico City (CST)", value: "America/Mexico_City" }
  ],
  Africa: [
    { label: "🇿🇦 Cape Town (SAST)", value: "Africa/Johannesburg" }
  ],
  Oceania: [
    { label: "🇦🇺 Sydney (AEST)", value: "Australia/Sydney" },
    { label: "🇳🇿 Auckland (NZST)", value: "Pacific/Auckland" }
  ]
};

function populateDropdown(selectElement) {
  for (const region in timeZoneGroups) {
    const optGroup = document.createElement("optgroup");
    optGroup.label = region;

    timeZoneGroups[region].forEach(zone => {
      const option = document.createElement("option");
      option.value = zone.value;
      option.textContent = zone.label;
      optGroup.appendChild(option);
    });

    selectElement.appendChild(optGroup);
  }
}

populateDropdown(document.getElementById("fromZone"));
populateDropdown(document.getElementById("toZone"));

function convertTime() {
  const fromZone = document.getElementById("fromZone").value;
  const toZone = document.getElementById("toZone").value;
  const inputTime = document.getElementById("inputTime").value;

  if (!inputTime || !fromZone || !toZone) {
    document.getElementById("result").textContent = "Please fill all fields.";
    return;
  }

  const [hours, minutes] = inputTime.split(":");
  const now = new Date();
  now.setHours(hours);
  now.setMinutes(minutes);

  const fromTime = new Date(now.toLocaleString("en-US", { timeZone: fromZone }));
  const toTime = new Date(fromTime.toLocaleString("en-US", { timeZone: toZone }));

  const formatted = toTime.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
  document.getElementById("result").textContent = `Converted Time: ${formatted}`;
}

document.addEventListener("DOMContentLoaded", () => {
  $('#fromZone').selectize();
  $('#toZone').selectize();
});

const weatherApiKey = '76cb379b46a04ae797364430250108';

function fetchWeather(cityName) {
  const url = `https://api.weatherapi.com/v1/current.json?key=${weatherApiKey}&q=${encodeURIComponent(cityName)}`;

  fetch(url)
    .then(response => response.json())
    .then(data => {
      const temp = data.current.temp_c;
      const condition = data.current.condition.text;
      const icon = data.current.condition.icon;

      document.getElementById('weather-box').innerHTML = `
        <img src="${icon}" alt="${condition}" />
        <span>${cityName}: ${temp}°C, ${condition}</span>
      `;
    })
    .catch(error => {
      console.error('Weather fetch error:', error);
      document.getElementById('weather-box').innerText = 'Weather data unavailable.';
    });
}


