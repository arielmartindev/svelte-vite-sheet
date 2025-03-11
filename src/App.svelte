<script>
  import { onMount } from "svelte";
  import axios from "axios";
  import Papa from "papaparse";

  let titles = ["#", "Fecha", "Cliente","Diseñador", "Cantidad","Color", "Archivo"];

  let api_data = [];
  let filtered_api_data = [];
  let ref = null;

  //let api_url="https://docs.google.com/spreadsheets/d/e/2PACX-1vQf4UCa5rxjlaMxyWyNZuKoU7OLiqbASR3bs8QS4R75E3JmgQSfO8FtyA5qH5dlg1PlLUj6plEHNMKE/pub?output=csv"
  let api_url="https://docs.google.com/spreadsheets/d/e/2PACX-1vScW7EEoexKUbLaMS7JpgA1icdQGPzgDPxPMhFM59tdeZbeWuZyxZyFMbBwIP9Y57y8FvP_vT2-UXcA/pub?output=csv"
  
  const list = async () => {
    return axios
      .get(api_url,
        {
          responseType: "blob",
        }
      )
      .then((response) => new Promise((resolve, reject) => {
          Papa.parse(response.data, {
            header: true,
            complete: result =>  resolve(result.data),
            error: (error) => reject(error.message) 
          })
        })
      );
  };

  

  const getProducts = async () => {
     const data = await list();
     api_data = data
     filtered_api_data = data
     console.log(data)
  }

  getProducts()

  const search_data = (value) => {
    filtered_api_data = api_data.filter(
      (item) =>
        item.cliente.toLowerCase().includes(value.toLowerCase()) ||
        item.fecha.toLowerCase().includes(value.toLowerCase())
    );
  };

  onMount(() => {
    ref.focus();
  });
</script>

<main class="container">
  <div class="row">

    <h1>Viis Planilla</h1>

    <input
      type="text"
      class="form-control bg-dark text-white my-4"
      placeholder="Buscar"
      on:keyup={({ target: { value } }) => search_data(value)}
      bind:this={ref}
    />

    <table class="table table-dark">

      <thead>
        <tr class="text-uppercase">
          {#each titles as title}
            <td>{title}</td>
          {/each}
        </tr>
      </thead>

      <tbody>
        {#each filtered_api_data as data, i}
          <tr>
            <td>{i + 1}</td>
            <td>

              <span>{data.fecha}</span>

              <!--<span class="text-muted text-uppercase ms-2">
                {data.Correos}
              </span>-->

            </td>

            <td>
              {data.cliente}
            </td>

            <td>
              {data.diseñador}
            </td>

            <td
              class={data.cantidad > 0
                ? "text-success"
                : "text-danger"}
            >
              {data.cantidad}
            </td>

            <td>
              {data.color}
            </td>

            <td class="text-center">
              <a href="{data.archivo}" target="_blank">

              <img
                src={data.archivo}
                alt={data.cliente}
                style="width: 5rem; background:white"
                class="img-fluid me-2"
              />
            </a>
            </td>
            <!-- <td>
              <a href="{data.id}">Detalle</a>
            </td> -->
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</main>

<style>
</style>
