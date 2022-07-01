<script>
    import { user } from "$lib/sessionStore";
    import { supabase } from "$lib/supabaseClient";
    import Auth from "$lib/Auth.svelte";
    import Expense from "$lib/Expense.svelte";
    import Tabelle from "$lib/Tabelle.svelte";

    let changes;
    user.set(supabase.auth.user());

    supabase.auth.onAuthStateChange((_, session) => {
        user.set(session.user);
    });

    function DrawTable(event) {
        changes++;
    }
</script>

<div class="container mt-5 mb-5 w-full">
    {#if $user}
        <div><Expense on:message={DrawTable} /></div>
        {#key changes}
            <div><Tabelle /></div>
        {/key}
    {:else}
        <Auth />
    {/if}
</div>
