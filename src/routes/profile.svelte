<script>
	import { supabase } from '$lib/supabaseClient'
	import { onMount } from 'svelte'
	import { goto } from '$app/navigation'
	import { user } from '$lib/session'
	import Avatar from '$lib/Avatar.svelte'

	export let userInfo
	let loading = true
	let username = null
	let website = null
	let avatar_url = null

	onMount(async () => {
		let user = supabase.auth.user()
		if (!user) {
			console.log('not logged in')
			goto('/')
		}
		userInfo = user.email
	})

	async function signOut() {
		try {
			const { error } = await supabase.auth.signOut()
			goto('/')
			if (error) throw error
		} catch (error) {
			console.log(error.message)
		} finally {
		}
	}

	async function getProfile() {
		try {
			loading = true
			const user = supabase.auth.user()

			let { data, error, status } = await supabase
				.from('profiles')
				.select(`username, website, avatar_url`)
				.eq('id', user.id)
				.single()

			if (error && status !== 406) throw error

			if (data) {
				username = data.username
				website = data.website
				avatar_url = data.avatar_url
			}
		} catch (error) {
			alert(error.message)
		} finally {
			loading = false
		}
	}

	async function updateProfile() {
		try {
			loading = true
			const user = supabase.auth.user()

			const updates = {
				id: user.id,
				username: 'maria',
				website,
				avatar_url: '0.6121971936981133.jpg',
				updated_at: new Date()
			}

			let { error } = await supabase.from('profiles').upsert(updates, {
				returning: 'minimal' // Don't return the value after inserting
			})

			if (error) throw error
		} catch (error) {
			alert(error.message)
		} finally {
			loading = false
		}
	}
</script>

<h1>Profile</h1>
Welcome back: {userInfo}<br />
{avatar_url}
<hr />
<Avatar bind:path={avatar_url} />

<button on:click={signOut}>SignOut</button><br />
<button on:click={getProfile}>Get Profile</button>
<button on:click={updateProfile}>Update Profile</button>
