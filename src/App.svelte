<script>
  import { onMount } from "svelte";
  import axios from "axios";
  import Papa from "papaparse";

  let titles = ["#", "Title", "Category", "description", "image", "price"];
  let products = [];
  let filteredProducts = [];
  let ref = null;

  const list = async () => {
    return axios
      .get(
        "https://docs.google.com/spreadsheets/d/e/2PACX-1vQf4UCa5rxjlaMxyWyNZuKoU7OLiqbASR3bs8QS4R75E3JmgQSfO8FtyA5qH5dlg1PlLUj6plEHNMKE/pub?output=csv",
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
     products = data
     filteredProducts = data
     console.log(data)
  }

  getProducts()

  const searchProduct = (value) => {
    filteredProducts = products.filter(
      (product) =>
        product.id.toLowerCase().includes(value.toLowerCase()) ||
        product.title.toLowerCase().includes(value.toLowerCase())
    );
  };

  onMount(() => {
    ref.focus();
  });
</script>

<main class="container">
  <div class="row">
    <h1>Viis Market (Google Sheet API)</h1>

    <input
      type="text"
      class="form-control bg-dark text-white my-4"
      placeholder="Search your coin"
      on:keyup={({ target: { value } }) => searchProduct(value)}
      bind:this={ref}
    />

    <table class="table table-dark">
      <thead>
        <tr>
          {#each titles as title}
            <td>{title}</td>
          {/each}
        </tr>
      </thead>
      <tbody>
        {#each filteredProducts as product, i}
          <tr>
            <td>{i + 1}</td>
            <td>
              <img
                src={product.image}
                alt={product.title}
                style="width: 5rem"
                class="img-fluid me-2"
              />
              <span>{product.title}</span>
              <span class="text-muted text-uppercase ms-2">
                {product.description}
              </span>
            </td>
            <td>
              {product.category}
            </td>
            <td
              class={product.description > 0
                ? "text-success"
                : "text-danger"}
            >
              {product.description}
            </td>
            <td>
              <a href="{product.image}" target="_blank">{product.image}</a>
            </td>
            <td>
              u$s {product.price}
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</main>

<style>
</style>
