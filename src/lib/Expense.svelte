<script>
  import { supabase } from "$lib/supabaseClient";
  import { user } from "$lib/sessionStore";
  import { createEventDispatcher } from "svelte";

  let category;
  let loading = false;
  let chosenCategory = 0;
  let chosenSubCategory = 0;
  let subcategory;
  let data2;
  let datum;
  let amount = 0;
  let anmerkung = "";

  const dispatch = createEventDispatcher();

  async function getSubCategories(chosenCategory) {
    try {
      loading = true;

      let { data, error, status } = await supabase
        .from("subcategory")
        .select()
        .eq("category", chosenCategory);

      if (error && status !== 406) throw error;

      if (data) {
        subcategory = data;
      }
    } catch (error) {
      alert(error.message);
    } finally {
      loading = false;
    }
  }

  async function getCategories() {
    try {
      loading = true;

      let { data, error, status } = await supabase.from("category").select();

      if (error && status !== 406) throw error;

      if (data) {
        category = data;
      }
    } catch (error) {
      alert(error.message);
    } finally {
      loading = false;
    }
  }

  async function handleSubmit() {
    try {
      loading = true;

      let { error, status } = await supabase.from("ausgabe").insert([
        {
          date: datum,
          subcategory: chosenSubCategory,
          amount: amount,
          note: anmerkung,
        },
      ]);
      if (error && status !== 406) {
        throw error;
      } else {
        amount = 0;
        anmerkung = "";
        dispatch("message", {
          text: "Reload Table",
        });
      }
    } catch (error) {
      alert(error.message);
    } finally {
      loading = false;
    }
  }

  const data = getCategories();

  $: data2 = getSubCategories(chosenCategory);
  $: datum = new Date(currentDateTime);

  let d = new Date();
  let currentDateTime =
    d.getFullYear() +
    "-" +
    ("0" + d.getMonth()).slice(-2) +
    "-" +
    ("0" + d.getDay()).slice(-2) +
    "T" +
    d.getHours() +
    ":" +
    d.getMinutes();
</script>

<div class="w-full flex ml-2">
  <div>
    <label
      for="date"
      class="block ml-4 mb-2 text-sm font-medium text-gray-900 dark:text-gray-400"
      >Datum</label
    >
    <input
      type="datetime-local"
      class="form-input form-input-lg px-4 py-2 appearance-none w-full text-xl border border-solid border-gray-300 rounded focus:text-gray-700 focus:bg-white focus:border-green-600 focus:outline-none"
      id="date"
      bind:value={currentDateTime}
    />
  </div>
  <div class="ml-2">
    <label
      for="amount"
      class="block ml-4 mb-2 text-sm font-medium text-gray-900 dark:text-gray-400"
      >Ausgabe</label
    >
    <input
      type="number"
      class="form-input form-input-lg px-4 py-2 appearance-none w-full text-xl border border-solid border-gray-300 rounded focus:text-gray-700 focus:bg-white ocus:border-green-600 focus:outline-none"
      id="amount"
      step="any"
      bind:value={amount}
    />
  </div>
  {#await data}
    Lade Daten ...
  {:then}
    <div class="">
      <label
        for="category"
        class="block ml-4 mb-2 text-sm font-medium text-gray-900 dark:text-gray-400"
        >Hauptkategorie</label
      >
      <select
        id="categories"
        class="form-select form-select-lg ml-2 mb-3 px-4 py-2 appearance-none w-full text-xl border border-solid border-gray-300 rounded focus:text-gray-700 focus:bg-white focus:border-green-600 focus:outline-none"
        aria-label=".form-select-lg example"
        bind:value={chosenCategory}
      >
        {#each category as cat}
          <option value={cat.id}>{cat.name}</option>
        {/each}
      </select>
    </div>
  {/await}

  {#await data2}
    Lade Daten ...
  {:then}
    <div class="ml-2">
      <label
        for="category"
        class="block ml-4 mb-2 text-sm font-medium text-gray-900 dark:text-gray-400"
        >Unterkategorie</label
      >
      <select
        id="subcategories"
        class="form-select form-select-lg mb-3 ml-2 px-4 py-2 appearance-none w-full text-xl border border-solid border-gray-300 rounded focus:text-gray-700 focus:bg-white focus:border-green-600 focus:outline-none"
        aria-label=".form-select-lg example"
        bind:value={chosenSubCategory}
      >
        {#each subcategory as cat}
          <option value={cat.id}>{cat.name}</option>
        {/each}
      </select>
    </div>
  {/await}
  <div class="ml-2">
    <label
      for="anmerkung"
      class="block ml-4 mb-2 text-sm font-medium text-gray-900 dark:text-gray-400"
      >Anmerkung</label
    >
    <input
      type="text"
      class="ml-2 form-input form-input-lg px-4 py-2 appearance-none w-full text-xl border border-solid border-gray-300 rounded focus:text-gray-700 focus:bg-white ocus:border-green-600 focus:outline-none"
      id="anmerkung"
      bind:value={anmerkung}
    />
  </div>
  <div class="ml-2">
    <button
      class="bg-green-500 hover:bg-green-700 text-white font-bold ml-2 mt-8 px-4 py-1 rounded text-xl"
      on:click={handleSubmit}
      disabled={loading}>Eintragen</button
    >
  </div>
</div>
