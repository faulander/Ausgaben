<script>
  import { supabase } from "$lib/supabaseClient";
  import { user } from "$lib/sessionStore";

  let categories;
  let subcategories;
  let ausgaben;

  async function getSubCategories() {
    try {
      const user = supabase.auth.user();

      let { data, error, status } = await supabase.from("subcategory").select();

      if (error && status !== 406) throw error;

      if (data) {
        subcategories = data;
        console.log(subcategories);
      }
    } catch (error) {
      alert(error.message);
    }
  }

  async function getCategories() {
    try {
      const user = supabase.auth.user();

      let { data, error, status } = await supabase.from("category").select();

      if (error && status !== 406) throw error;

      if (data) {
        categories = data;
        console.log(categories);
      }
    } catch (error) {
      alert(error.message);
    }
  }

  async function getAusgaben() {
    try {
      const user = supabase.auth.user();

      let { data, error, status } = await supabase
        .from("ausgabe")
        .select()
        .order("created_at", { ascending: false });
      if (error && status !== 406) throw error;

      if (data) {
        ausgaben = data;
      }
    } catch (error) {
      alert(error.message);
    }
  }

  const daten = Promise.allSettled([
    getCategories(),
    getSubCategories(),
    getAusgaben(),
  ]);
</script>

{#await daten}
  LADE DATEN ...
{:then}
  <div class="w-full mt-5 ml-2">
    <table class="table-auto w-full text-left">
      <thead>
        <tr>
          <th>Datum</th>
          <th>Anmerkung</th>
          <th class="text-right pr-2">Betrag</th>
          <th>Hauptkategorie</th>
          <th>Unterkategorie</th>
          <th>&nbsp;</th>
        </tr>
      </thead>
      <tbody>
        {#each ausgaben as ausg}
          <tr>
            <td
              >{new Date(Date.parse(ausg.date)).toLocaleDateString("de-DE")}, {new Date(
                Date.parse(ausg.date)
              ).toLocaleTimeString("de-DE")}
            </td>
            <td>{ausg.note}</td>
            <td class="text-right pr-2">{ausg.amount.toFixed(2)}</td>
            <td
              >{categories.find(
                ({ id }) =>
                  id ===
                  subcategories.find(({ id }) => id === ausg.subcategory)
                    .category
              ).name}</td
            >
            <td
              >{subcategories.find(({ id }) => id === ausg.subcategory)
                .name}</td
            >
            <td
              ><div class="flex">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="h-6 w-6"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                  stroke-width="2"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
                  />
                </svg>
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="h-6 w-6"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                  stroke-width="2"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M15.232 5.232l3.536 3.536m-2.036-5.036a2.5 2.5 0 113.536 3.536L6.5 21.036H3v-3.572L16.732 3.732z"
                  />
                </svg>
              </div></td
            >
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
{/await}
