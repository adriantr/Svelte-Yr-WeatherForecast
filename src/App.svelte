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
    }
  ]
  let tab = [];
  let dates = [];

  let infos = [];

  for (let index = 0; index < 8; index++) {
    let newDate = new Date();
    newDate.setDate(newDate.getDate() + index);
    newDate.setUTCMinutes(0);
    newDate.setUTCMilliseconds(0);
    newDate.setUTCSeconds(0);
    newDate.setUTCHours(12);
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

		infos.push(info);
		
		infos = infos;
      });
  }

  console.log(infos);
</script>

<table class="table-auto border-separate">
  <tr class="bg-gray-100">
    <th class="px-4 py-2">City</th>
    {#each dates as date}
      <th class="px-4 py-2">{new Date(date).toLocaleString()}</th>
    {/each}
  </tr>
  {#each infos as city}
    <tr>
      <td>{city.name}</td>
      {#each city.timeseries as ts}
        <td class="{ts.instant.details.air_temperature < 15 ? 'bg-blue-100' : 'bg-red-100'} rounded-lg"><div class="flex-row"><div>{ts.instant.details.air_temperature}</div><img src="{symbols[ts.next_6_hours.summary.symbol_code]}"></div></td>
      {/each}
    </tr>
  {/each}
</table>
