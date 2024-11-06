<script lang="ts">
	import { onMount } from 'svelte';

	let email = 'user@example.com';
	let password = 'password';

	function handleLogin() {
		console.log('Logging in with', email, password);
		window.location.href = '/project2/dashboard'; 
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

/*
@media (max-width: 750px) {
  .content {
     flex-direction: column-reverse;
  }
  
  .btn {
    margin: 10px auto 40px;
  }
}
*/

.shapes {
    position: absolute;
    bottom: 20px;
    left: 0;
    width: 100%;
    height: 100px;
    pointer-events: none;
    z-index: 1;
}

.image-container {
    position: relative;
    z-index: 2;
}
</style>
<div class="flex flex-col min-h-screen md:flex-row">
    <div class="flex-1 bg-green-700 flex items-center justify-center relative">
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
        <div class="shapes">
            <div class="shape1"></div>
            <div class="shape2"></div>
        </div>
        <div class="image-container">
            <img src="./ESS.png" alt="Fallback description if image fails to load" class="max-w-full h-auto" aria-hidden="true" />
        </div>
    </div>
    <div class="flex-1 flex items-center justify-center">
        <form on:submit|preventDefault={handleSubmit} class="p-6 w-full md:w-2/3">
            <h2 class="text-2xl font-bold mb-4 text-center">Log In</h2>
            <div class="mb-4">
                <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
                <input type="email" id="email" bind:value={email} required class="mt-1 block w-full p-2 border border-gray-300 rounded" />
            </div>
            <div class="mb-4">
                <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
                <input type="password" id="password" bind:value={password} required class="mt-1 block w-full p-2 border border-gray-300 rounded" />
            </div>
            <button type="submit" class="w-full bg-green-500 text-white p-2 rounded hover:bg-green-600" on:click={handleLogin}>Log In</button>
            <p class="mt-4 text-center">Don't Have Any Account? 
                <button type="button" class="text-red-500" on:click={() => window.location.href='/projec/register'}>Register</button>
            </p>
        </form>
    </div>
</div>
