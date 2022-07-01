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
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
{/await}
