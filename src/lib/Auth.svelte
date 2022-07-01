<script>
    import { supabase } from "$lib/supabaseClient";

    let loading = false;
    let email, password;
    let loadingSignup = false;
    let emailSignup, passwordSignup;

    const handleLogin = async () => {
        try {
            loading = true;
            const { error } = await supabase.auth.signIn({ email, password });
            if (error) throw error;
        } catch (error) {
            alert(error.error_description || error.message);
        } finally {
            loading = false;
        }
    };
    const handleSignup = async () => {
        console.log(passwordSignup);
        console.log(emailSignup);
        try {
            loadingSignup = true;
            const { error } = await supabase.auth.signUp({
                email: emailSignup,
                password: passwordSignup,
            });
            if (error) throw error;
        } catch (error) {
            alert(error.error_description || error.message);
        } finally {
            loadingSignup = false;
        }
    };
</script>

<form class="" on:submit|preventDefault={handleLogin}>
    <div class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4 flex flex-col">
        <div class="mb-4">
            <label
                class="block text-grey-darker text-sm font-bold mb-2"
                for="username"
            >
                Username
            </label>
            <input
                class="shadow appearance-none border rounded w-full py-2 px-3 text-grey-darker"
                id="email"
                type="email"
                placeholder="Email"
                bind:value={email}
            />
        </div>
        <div class="mb-6">
            <label
                class="block text-grey-darker text-sm font-bold mb-2"
                for="password"
            >
                Password
            </label>
            <input
                class="shadow appearance-none border border-red rounded w-full py-2 px-3 text-grey-darker mb-3"
                id="password"
                type="password"
                placeholder="******************"
                bind:value={password}
            />
        </div>
        <div class="flex items-center justify-between">
            <button
                type="submit"
                class="bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded"
                disabled={loading}>{loading ? "Loading" : "Sign In"}</button
            >
        </div>
    </div>
</form>

<form class="" on:submit|preventDefault={handleSignup}>
    <div class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4 flex flex-col">
        <div class="mb-4">
            <label
                class="block text-grey-darker text-sm font-bold mb-2"
                for="username"
            >
                Email
            </label>
            <input
                class="shadow appearance-none border rounded w-full py-2 px-3 text-grey-darker"
                id="emailSignup"
                type="email"
                placeholder="Email"
                bind:value={emailSignup}
            />
        </div>
        <div class="mb-6">
            <label
                class="block text-grey-darker text-sm font-bold mb-2"
                for="password"
            >
                Password
            </label>
            <input
                class="shadow appearance-none border border-red rounded w-full py-2 px-3 text-grey-darker mb-3"
                id="passwordSignup"
                type="password"
                placeholder="******************"
                bind:value={passwordSignup}
            />
        </div>
        <div class="flex items-center justify-between">
            <button
                type="submit"
                class="bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded"
                disabled={loadingSignup}
                >{loadingSignup ? "Loading" : "Sign Up"}</button
            >
        </div>
    </div>
</form>
