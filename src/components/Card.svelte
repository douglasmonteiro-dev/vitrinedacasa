<script>
  export let link;
  export let name;
  export let user;
  import { onMount } from "svelte";
  import axios from "axios";
  const redirect = async () => {
    await axios
      .post("/api/user/clickadd", { instagram: name, _id: link._id })
      .then((res) => console.log("done", res));
  };
  let styles = {};
  onMount(async () => {
    styles['primary_color'] = user.style.primary_color;
  });
  $: cssVarStyles = Object.entries(styles)
		.map(([key, value]) => `--${key}:${value}`)
		.join(';');
</script>
<style>
  .btn-primary {
    background: var(--primary_color, #007bff)!important;
    border-color: var(--primary_color, #007bff)!important;
    width: 100%;
  }
</style>
<div style="{cssVarStyles}">
  <div class="card sankarcard">
    {#if link.image !== null && link.image !== ""}
      <img
        class="card-img-top"
        style="max-height: 375px;"
        src={link.image}
        alt="Not available"
      />
    {/if}
    <div class="card-header">
      <h5 class="card-title mb-0">{link.title}</h5>
    </div>
    <hr class="mb-0" />
    <div class="card-body">
      <p class="card-text">
        {link.description.substr(0, 100) + "..."}
      </p>
      <a
        on:click={redirect}
        href={link.url}
        target="_blank"
        class="btn btn-primary">Agendar</a
      >
    </div>
  </div>
</div>
