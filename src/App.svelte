<script>
  import axios from "axios";
  let tab = [];
  let dates = [];
  let cities = [
    {
      name: "Oslo",
      lat: "59.9127",
      lon: "10.7461",
    },
  ];
  let infos = [];

  for (let index = 0; index < 8; index++) {
    let newDate = new Date();
    newDate.setDate(newDate.getDate() + index);
    newDate.setUTCMinutes(0);
    newDate.setUTCMilliseconds(0);
    newDate.setUTCSeconds(0);
    newDate.setUTCHours(0);
    dates.push(newDate.toISOString());
    newDate.setUTCHours(6);
    dates.push(newDate.toISOString());
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

<table class="table-auto">
  <tr>
    <td>City</td>
    {#each dates as date}
      <td>{new Date(date).toLocaleString()}</td>
    {/each}
  </tr>
  {#each infos as city}
    <tr>
      <td>{city.name}</td>
      {#each city.timeseries as ts}
        <td>{ts.instant.details.air_temperature}</td>
      {/each}
    </tr>
  {/each}
</table>
