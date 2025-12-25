<script lang="ts">
	let phoneNumber = $state('');
	let password = $state('');
	let confirmPassword = $state('');
	let inviteCode = $state('');
	let isLoading = $state(false);
	let errorMessage = $state('');
	let showPasswordRules = $state(false);

	// Password validation rules
	const passwordValidation = $derived({
		length: password.length >= 8 && password.length <= 20,
		hasLetters: /[a-zA-Z]/.test(password),
		hasNumbers: /[0-9]/.test(password),
		noSpacesOrPercent: password.length > 0 && !/[\s%]/.test(password)
	});

	const isPasswordValid = $derived(
		Boolean(
			passwordValidation.length &&
				passwordValidation.hasLetters &&
				passwordValidation.hasNumbers &&
				passwordValidation.noSpacesOrPercent
		)
	);

	const passwordsMatch = $derived(password === confirmPassword && confirmPassword.length > 0);

	const isFormValid = $derived(
		Boolean(phoneNumber.length >= 10 && isPasswordValid && passwordsMatch)
	);

	async function handleRegister() {
		if (!isFormValid) return;

		isLoading = true;
		errorMessage = '';

		try {
			const response = await fetch('https://dollar-pay-backend.onrender.com/register', {
				method: 'POST',
				headers: {
					accept: 'application/json',
					'Content-Type': 'application/json'
				},
				body: JSON.stringify({
					phone_number: phoneNumber,
					password: password
				})
			});

			if (!response.ok) {
				const data = await response.json();
				throw new Error(data.detail || 'Registration failed');
			}

			const data = await response.json();
			alert('Registration successful!');
		} catch (error) {
			errorMessage = error instanceof Error ? error.message : 'Registration failed';
		} finally {
			isLoading = false;
		}
	}
</script>

<div class="min-h-screen bg-gray-50 flex flex-col items-center px-4 py-8">
	<h1 class="text-xl font-semibold text-blue-500 mb-2">Register</h1>
	<div class="w-full max-w-md border-b border-gray-200 mb-8"></div>

	<form onsubmit={(e) => { e.preventDefault(); handleRegister(); }} class="w-full max-w-md space-y-4">
		<!-- Phone Number -->
		<div class="bg-white rounded-xl shadow-sm border border-gray-100 px-4 py-3 flex items-center gap-3">
			<svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
				<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"></path>
			</svg>
			<input
				type="tel"
				bind:value={phoneNumber}
				placeholder="Enter Phone Number"
				class="flex-1 outline-none text-gray-800 placeholder-gray-400 bg-transparent"
			/>
		</div>

		<!-- Password -->
		<div class="bg-white rounded-xl shadow-sm border border-gray-100 px-4 py-3">
			<div class="flex items-center gap-3">
				<svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
					<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"></path>
				</svg>
				<input
					type="password"
					bind:value={password}
					placeholder="Enter Password"
					onfocus={() => (showPasswordRules = true)}
					class="flex-1 outline-none text-gray-800 placeholder-gray-400 bg-transparent"
				/>
			</div>

			{#if showPasswordRules}
				<div class="mt-3 space-y-1 text-sm">
					<div class="flex items-center gap-2">
						{#if passwordValidation.length}
							<span class="text-green-500">✓</span>
						{:else}
							<span class="text-red-500">✗</span>
						{/if}
						<span class={passwordValidation.length ? 'text-green-500' : 'text-red-500'}>
							Password must be at least 8 characters and less than 20 characters
						</span>
					</div>
					<div class="flex items-center gap-2">
						{#if passwordValidation.hasLetters}
							<span class="text-green-500">✓</span>
						{:else}
							<span class="text-red-500">✗</span>
						{/if}
						<span class={passwordValidation.hasLetters ? 'text-green-500' : 'text-red-500'}>
							Contains letters(a-zA-Z)
						</span>
					</div>
					<div class="flex items-center gap-2">
						{#if passwordValidation.hasNumbers}
							<span class="text-green-500">✓</span>
						{:else}
							<span class="text-red-500">✗</span>
						{/if}
						<span class={passwordValidation.hasNumbers ? 'text-green-500' : 'text-red-500'}>
							Contains numbers(0-9)
						</span>
					</div>
					<div class="flex items-center gap-2">
						{#if passwordValidation.noSpacesOrPercent}
							<span class="text-green-500">✓</span>
						{:else}
							<span class="text-red-500">✗</span>
						{/if}
						<span class={passwordValidation.noSpacesOrPercent ? 'text-green-500' : 'text-red-500'}>
							No spaces or % symbol allowed
						</span>
					</div>
				</div>
			{/if}
		</div>

		<!-- Confirm Password -->
		<div class="bg-white rounded-xl shadow-sm border border-gray-100 px-4 py-3 flex items-center gap-3">
			<svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
				<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"></path>
			</svg>
			<input
				type="password"
				bind:value={confirmPassword}
				placeholder="Confirm Password"
				class="flex-1 outline-none text-gray-800 placeholder-gray-400 bg-transparent"
			/>
		</div>

		<!-- Invite Code -->
		<div class="bg-white rounded-xl shadow-sm border border-gray-100 px-4 py-3 flex items-center gap-3">
			<svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
				<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"></path>
			</svg>
			<input
				type="text"
				bind:value={inviteCode}
				placeholder="Invite Code"
				class="flex-1 outline-none text-gray-800 placeholder-gray-400 bg-transparent"
			/>
		</div>

		{#if errorMessage}
			<div class="text-red-500 text-sm text-center">{errorMessage}</div>
		{/if}

		<!-- Register Button -->
		<button
			type="submit"
			disabled={!isFormValid || isLoading}
			class="w-full py-4 rounded-xl text-white font-medium transition-all
				{isFormValid && !isLoading
					? 'bg-blue-400 hover:bg-blue-500 cursor-pointer'
					: 'bg-blue-300 cursor-not-allowed'}"
		>
			{isLoading ? 'Registering...' : 'Register'}
		</button>
	</form>
</div>
