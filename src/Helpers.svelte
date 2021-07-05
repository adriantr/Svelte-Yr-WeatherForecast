<script context="module">
  export function getDates() {
    let dates = [];

    let now = new Date();

    for (let index = 0; index < 8; index++) {
      let newDate = new Date();
      newDate.setDate(newDate.getDate() + index);
      newDate.setUTCMinutes(0);
      newDate.setUTCMilliseconds(0);
      newDate.setUTCSeconds(0);
      newDate.setUTCHours(12);
      if (index == 0) {
        if (now.getUTCHours() < 12) {
          dates.push(newDate.toISOString());
          newDate.setUTCHours(18);
          dates.push(newDate.toISOString());
        } else if (now.getUTCHours() < 18) {
          newDate.setUTCHours(18);
          dates.push(newDate.toISOString());
        }
      } else {
        dates.push(newDate.toISOString());
        newDate.setUTCHours(18);
        dates.push(newDate.toISOString());
      }
    }

    return dates;
  }

  //   export async function getWeatherData(cities, dates) {
  //     let infos = [];

  //     for (let city of cities) {
  //       let info = { name: city.name, timeseries: [] };
  //       axios
  //         .get(
  //           `https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=${city.lat}&lon=${city.lon}`
  //         )
  //         .then(function (resp) {
  //           for (let ts of resp.data.properties.timeseries) {
  //             if (dates.find((x) => x == new Date(ts.time).toISOString())) {
  //               info.timeseries.push(ts.data);
  //             }
  //           }

  //           info.average =
  //             info.timeseries.reduce((acc, obj) => {
  //               acc = acc + obj.instant.details.air_temperature;
  //               return acc;
  //             }, 0) / info.timeseries.length;

  //           infos.push(info);

  //           infos.sort(function (a, b) {
  //             return b.average - a.average;
  //           });
  //           infos = infos;
  //         });
  //     }
  //     return infos
  //   }

  export async function getWeatherData(cities, dates) {
    let infos = [];

    for (let city of cities) {
      let dt = await getDataFromYr(city, dates);
      infos.push(dt);
    }

    infos.sort(function (a, b) {
      return b.average - a.average;
    });

    return infos;
  }

  async function getDataFromYr(city, dates) {
    let info = { name: city.name, timeseries: [] };

    const res = await fetch(
      `https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=${city.lat}&lon=${city.lon}`
    );
    let resp = await res.json();

    for (let ts of resp.properties.timeseries) {
      if (dates.find((x) => x == new Date(ts.time).toISOString())) {
        info.timeseries.push(ts.data);
      }
    }

    info.average =
      info.timeseries.reduce((acc, obj) => {
        acc = acc + obj.instant.details.air_temperature;
        return acc;
      }, 0) / info.timeseries.length;

    return info;
  }
</script>

<script>
  import Axios from "axios";
</script>
