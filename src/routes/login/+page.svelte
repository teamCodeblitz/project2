<script lang="ts">
	import { onMount } from 'svelte';

	let email = 'user@example.com';
	let password = 'password';
	let isLoading = false; // New state variable to control loader visibility

	function handleLogin() {
		console.log('Logging in with', email, password);
		
		isLoading = true; // Set loading state to true

		// Redirect after 5 seconds
		setTimeout(() => {
			isLoading = false; // Reset loading state
			window.location.href = '/project2/dashboard'; 
		}, 3000);
	}

	function handleSubmit() {
		handleLogin(); 
	}
</script>
<style> 
    
/* ANIMATED BACKGROUND */

.circles {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.circles li {
  position: absolute;
  display: block;
  list-style: none;
  width: 20px;
  height: 20px;
  background-color: #838383;
  animation: animate 25s linear infinite;
  bottom: -150px;
}

.circles li:nth-child(1) {
  left: 25%;
  width: 80px;
  height: 80px;
  animation-delay: 0s;
}

.circles li:nth-child(2) {
  left: 10%;
  width: 20px;
  height: 20px;
  animation-delay: 2s;
  animation-duration: 12s;
}

.circles li:nth-child(3) {
  left: 70%;
  width: 20px;
  height: 20px;
  animation-delay: 4s;
}

.circles li:nth-child(4) {
  left: 40%;
  width: 60px;
  height: 60px;
  animation-delay: 0s;
  animation-duration: 18s;
}

.circles li:nth-child(5) {
  left: 65%;
  width: 20px;
  height: 20px;
  animation-delay: 0s;
}

.circles li:nth-child(6) {
  left: 75%;
  width: 110px;
  height: 110px;
  animation-delay: 3s;
}

.circles li:nth-child(7) {
  left: 35%;
  width: 150px;
  height: 150px;
  animation-delay: 7s;
}

.circles li:nth-child(8) {
  left: 50%;
  width: 25px;
  height: 25px;
  animation-delay: 15s;
  animation-duration: 45s;
}

.circles li:nth-child(9) {
  left: 20%;
  width: 15px;
  height: 15px;
  animation-delay: 2s;
  animation-duration: 35s;
}

.circles li:nth-child(10) {
  left: 85%;
  width: 150px;
  height: 150px;
  animation-delay: 0s;
  animation-duration: 11s;
}

@keyframes animate {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 1;
    border-radius: 0;
  }

  100% {
    transform: translateY(-1000px) rotate(720deg);
    opacity: 0;
    border-radius: 50%;
  }
}

.image-container {
    position: relative;
    z-index: 2;
}

/* Add this CSS to hide the loader on mobile */
@media (max-width: 750px) {
   .image-container, .add, .font-bold, .left-section {
        display: none;
    }
}
</style>

{#if isLoading} <!-- Conditional rendering for loader -->
<div class="absolute inset-0 flex items-center justify-center bg-white bg-opacity-75 z-50">
    
<div role="status">
    <svg fill="currentColor" viewBox="0 0 1792 1792" class="text-green-500 w-20 h-20 animate-spin"
    xmlns="http://www.w3.org/2000/svg">
    <path
        d="M1760 896q0 176-68.5 336t-184 275.5-275.5 184-336 68.5-336-68.5-275.5-184-184-275.5-68.5-336q0-213 97-398.5t265-305.5 374-151v228q-221 45-366.5 221t-145.5 406q0 130 51 248.5t136.5 204 204 136.5 248.5 51 248.5-51 204-136.5 136.5-204 51-248.5q0-230-145.5-406t-366.5-221v-228q206 31 374 151t265 305.5 97 398.5z" />
</svg>
    <span class="sr-only">Loading...</span>
</div>

</div>
{/if}
<div class="flex flex-col min-h-screen md:flex-row">
    <div class="flex-1 bg-green-700 flex items-center justify-center relative left-section">
       
        <ul class="circles">
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
        </ul>
        <div class="image-container">
            <img src="./ESS.png" alt="Fallback description if image fails to load" class="max-w-full h-auto" aria-hidden="true" />
        </div>
        <div class="absolute bottom-10 left-10 text-white text-lg">
            <h1 class="font-bold">Welcome to Event Scheduling System</h1>
            <p class="add">Your gateway to a seamless experience. Join us and explore endless possibilities!</p>
        </div>
    </div>
    <div class="flex-1 flex items-center justify-center">
        <form on:submit|preventDefault={handleSubmit} class="p-6 w-full md:w-2/3">
            <h2 class="text-3xl font-extrabold mb-4 text-center text-gradient">Welcome Back to Your Journey!</h2>
            <p class="text-center mb-6 text-gray-600">Please enter your credentials to access your account.</p>
            <div class="mb-4">
                <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
                <input type="email" id="email" bind:value={email} required class="mt-1 block w-full p-2 border border-gray-300 rounded" />
            </div>
            <div class="mb-4">
                <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
                <input type="password" id="password" bind:value={password} required class="mt-1 block w-full p-2 border border-gray-300 rounded" />
            </div>
            <button type="submit" class="w-full bg-green-500 text-white p-2 rounded hover:bg-green-600">Log In</button>
            <p class="mt-4 text-center">Don't Have an Account? 
                <button type="button" class="text-red-500" on:click={() => window.location.href='/project2/register'}>Register</button>
            </p>
            <p class="mt-2 text-center text-sm text-gray-500">Version 1.0.0 Developed by Team Codeblitz</p>
        </form>
    </div>
</div>
