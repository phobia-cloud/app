<script lang="ts">
	import TrezorConnect from 'trezor-connect';

	let status = '';

	TrezorConnect.manifest({
		email: 'kaloyan@raev.name',
		appUrl: 'https://phobia.cloud'
	});

	async function login(): Promise<void> {
		status = 'Signing in...';

		console.log('Requesting challenge from server...');
		const response = await fetch("http://localhost:5050/challenge");
		if (!response.ok) {
			status = `Request of challenge hidden failed: ${response.statusText}`;
			return
		}
		const loginChallenge = await response.json();
		console.log('Server response: %s', JSON.stringify(loginChallenge));

		console.log('Requesting Trezor to sign the challenge: %s', JSON.stringify(loginChallenge));
		const result = await TrezorConnect.requestLogin(loginChallenge);
		console.log('Trezor response: %s', JSON.stringify(result));
		if (!result.success) {
			status = `Trezor sign in failed: ${result.payload.error} (${result.payload.code})`;
			return
		}

		const serverLoginRequest = {
			challengeHidden: loginChallenge.challengeHidden,
			challengeVisual: loginChallenge.challengeVisual,
			publicKey: result.payload.publicKey,
			signature: result.payload.signature,
			version: 2
		};
		console.log('Logging in to server: %s', JSON.stringify(serverLoginRequest));
		const loginResp = await fetch("http://localhost:5050/login", {
			method: 'post',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify(serverLoginRequest)
		});
		console.log("Server response: %d", loginResp.status);

		status = `Login response code: ${loginResp.status}`;
	}
</script>

<div class="login">
	<button on:click={login}>Sign in with Trezor</button>
	<p>{status}</p>
</div>
