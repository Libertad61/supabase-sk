<script>
	import { supabase } from '$lib/supabaseClient'
	import { user } from '$lib/session'
	import { goto } from '$app/navigation'
	//console.log(supabase)
	let loading = false
	const signup = async () => {
		try {
			loading = true
			const { error } = await supabase.auth.signUp({
				email: 'maria@g.com',
				password: '123456'
			})
		} catch (error) {
			alert(error.error_description || error.message)
		} finally {
			loading = false
		}
	}
	const signin = async () => {
		try {
			loading = true
			const { user, error } = await supabase.auth.signIn({
				email: 'maria@g.com',
				password: '123456'
			})
			if (user) {
				goto('/profile')
			}
		} catch (error) {
			alert(error.error_description || error.message)
		} finally {
			loading = false
		}
	}
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
<button on:click={signin}>Login</button>
