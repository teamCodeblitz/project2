<script>
    let email = '';
    let password = '';
    
    const handleSubmit = () => {
        // Handle form submission
        console.log('Email:', email);
        console.log('Password:', password);
    };
</script>

<div class="flex min-h-screen">
    <div class="flex-1 bg-green-800 flex items-center justify-center">
        <img src="image1.png" alt="Fallback description if image fails to load" class="max-w-full h-auto" aria-hidden="true" />
    </div>
    <div class="flex-1 flex items-center justify-center">
        <form on:submit|preventDefault={handleSubmit} class="p-6 w-2/3">
            <h2 class="text-2xl font-bold mb-4 text-center">Register</h2>
            <div class="mb-4">
                <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
                <input type="email" id="email" bind:value={email} required class="mt-1 block w-full p-2 border border-gray-300 rounded" />
            </div>
            <div class="mb-4">
                <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
                <input type="password" id="password" bind:value={password} required class="mt-1 block w-full p-2 border border-gray-300 rounded" />
            </div>
            <button type="submit" class="w-full bg-green-500 text-white p-2 rounded hover:bg-green-600">Register</button>
            <p class="mt-4 text-center">Already Have an Account? <button type="button" class="text-red-500" on:click={() => window.location.href='/project2/login'}>Login Now</button></p>
        </form>
    </div>
</div>
>