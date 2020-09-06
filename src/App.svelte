<script>
  import axios from "axios";
  import { getSymbols, getPlaces } from "./Constants.svelte";
  import { getDates, getWeatherData } from "./Helpers.svelte";
  import { onMount } from 'svelte'

  let symbols = getSymbols();
  let cities = getPlaces();
  let dates = getDates();
  let infos = [];
  onMount(async () => {
		infos = await getWeatherData(cities, dates)
	});
</script>

<table class="table-auto border-separate">
  <tr class="bg-gray-100">
    <th class="px-4 py-2">City</th>
    {#each dates as date}
      <th class="px-4 py-2">
        {new Date(date).toLocaleString('nb-NO', {
          weekday: 'long',
          day: 'numeric',
          month: 'short',
          hour: '2-digit',
          minute: '2-digit',
        })}
      </th>
    {/each}
  </tr>
  {#each infos as city}
    <tr>
      <td>
        <div class="flex-row">
          <div class="flex justify-center font-bold">{city.name}</div>
          <div class="flex justify-center font-thin text-xs">
            Avg: {city.average.toFixed(1)}
          </div>
        </div>
      </td>
      {#each city.timeseries as ts}
        <td
          class="{ts.instant.details.air_temperature < 15 ? 'bg-blue-100 hover:bg-blue-200' : 'bg-red-100 hover:bg-red-200'}
            rounded-lg">
          <div class="flex-row">
            <div class="flex justify-center font-bold">
              T: {ts.instant.details.air_temperature}
            </div>
            <div class="flex justify-center font-thin text-xs">
              W: {ts.instant.details.wind_speed}
            </div>
            <div class="flex justify-center">
              <img
                class="h-10 w-10"
                src={symbols[ts.next_6_hours.summary.symbol_code]} />
            </div>
          </div>
        </td>
      {/each}
    </tr>
  {/each}
</table>
