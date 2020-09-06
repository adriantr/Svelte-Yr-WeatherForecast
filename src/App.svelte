<script>
  import axios from "axios";
  let symbols = {
    clearsky_day: "http://nrkno.github.io/yr-weather-symbols/png/100/01d.png",
    partlycloudy_day: "http://nrkno.github.io/yr-weather-symbols/png/100/03d.png",
    cloudy: "http://nrkno.github.io/yr-weather-symbols/png/100/04.png",
    fair_day: "http://nrkno.github.io/yr-weather-symbols/png/100/02d.png",
    rain: "http://nrkno.github.io/yr-weather-symbols/png/100/09.png",
    clearsky_night: "http://nrkno.github.io/yr-weather-symbols/png/100/01n.png",
    fair_night: "http://nrkno.github.io/yr-weather-symbols/png/100/02n.png",
    partlycloudy_night: "http://nrkno.github.io/yr-weather-symbols/png/100/03n.png",
    lightrain: "http://nrkno.github.io/yr-weather-symbols/png/100/46.png",
    rainshowers_day: "http://nrkno.github.io/yr-weather-symbols/png/100/05d.png",
    heavyrain: "http://nrkno.github.io/yr-weather-symbols/png/100/10.png",
    heavyrainshowers_night: "http://nrkno.github.io/yr-weather-symbols/png/100/41n.png",
    heavyrainshowers_day: "http://nrkno.github.io/yr-weather-symbols/png/100/41d.png",
    rainshowers_night: "http://nrkno.github.io/yr-weather-symbols/png/100/05n.png"
  };

  const cities = [
    {
      name: "Oslo",
      lat: "59.9127",
      lon: "10.7461",
    },
    {
      name: "Bodø",
      lat: "67.2827",
      lon: "14.3751"
    },
    {
      name: "Tromsø",
      lat: "69.6489",
      lon: "18.9551"
    },
    {
      name: "Stavanger",
      lat: "58.9653",
      lon: "5.7180"
    },
    {
      name: "Larvik",
      lat: "59.0533",
      lon: "10.0352"
    },
    {
      name: "Ryfoss",
      lat: "61.1378",
      lon: "8.8194"
    },
    {
      name: "Svolvær",
      lat: "68.2353",
      lon: "14.5636"
    },
    {
      name: "Kristiansand",
      lat: "58.1461",
      lon: "7.9957"
    },
    {
      name: "Gotland",
      lat: "57.5000",
      lon: "18.5000"
    },
    {
      name: "Beitostølen",
      lat: "61.2486",
      lon: "8.9065"
    },
    {
      name: "Hamn (Senja)",
      lat: "69.4173", 
      lon: "17.1623"
    },
    {
      name: "Stryn/Loen",
      lat: "61.9026",
      lon: "6.7179"
    },
    {
      name: "Geiranger",
      lat: "62.0996",
      lon: "7.2066"
    },
    {
      name: "Nærøyfjorden",
      lat: "60.8949",
      lon: "6.8595"
    },
    {
      name: "Vesterålen",
      lat: "68.8364",
      lon: "14.5414"
    },
    {
      name: "Lyngsalpene",
      lat: "69.8763",
      lon: "20.1387"
    },
    {
      name: "Saltfjellet",
      lat: "66.5839",
      lon: "15.3458"
    }
  ]
  let tab = [];
  let dates = [];

  let infos = [];

  for (let index = 0; index < 8; index++) {
    let newDate = new Date();
    newDate.setDate(newDate.getDate() + index);
    newDate.setUTCMinutes(0); newDate.setUTCMilliseconds(0); newDate.setUTCSeconds(0); newDate.setUTCHours(12);
    dates.push(newDate.toISOString());
    newDate.setUTCHours(18);
    dates.push(newDate.toISOString());
  }

  for (let city of cities) {
    let info = { name: city.name, timeseries: [] };
    axios
      .get(
        `https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=${city.lat}&lon=${city.lon}`
      )
      .then(function (resp) {
        for (let ts of resp.data.properties.timeseries) {
          if (dates.find((x) => x == new Date(ts.time).toISOString())) {
            info.timeseries.push(ts.data);
          }
        }

    
    info.average = info.timeseries.reduce((acc, obj) => {acc = acc + obj.instant.details.air_temperature; return acc},0)/info.timeseries.length;

		infos.push(info);
    
    
    infos.sort(function (a, b) {
    return b.average - a.average;
    });
		infos = infos;
      });
  }
</script>

<table class="table-auto border-separate">
  <tr class="bg-gray-100">
    <th class="px-4 py-2">City</th>
    {#each dates as date}
      <th class="px-4 py-2">{new Date(date).toLocaleString('nb-NO', {weekday: "long", day: "numeric", month: "short", hour: "2-digit", minute: "2-digit"})}</th>
    {/each}
  </tr>
  {#each infos as city}
    <tr>
      <td>
        <div class="flex-row">
          <div class="flex justify-center font-bold">{city.name}</div>
          <div class="flex justify-center font-thin text-xs">Avg: {city.average.toFixed(1)}</div>
        </div>
      </td>
      {#each city.timeseries as ts}
        <td class="{ts.instant.details.air_temperature < 15 ? 'bg-blue-100 hover:bg-blue-200' : 'bg-red-100 hover:bg-red-200'} rounded-lg">
          <div class="flex-row">
            <div class="flex justify-center font-bold">T: {ts.instant.details.air_temperature}</div>
            <div class="flex justify-center font-thin text-xs">W: {ts.instant.details.wind_speed}</div>
            <div class="flex justify-center">
              <img class="h-10 w-10" src="{symbols[ts.next_6_hours.summary.symbol_code]}">
            </div>
          </div>
        </td>
      {/each}
    </tr>
  {/each}
</table>
