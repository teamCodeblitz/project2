<!-- src/routes/headbar/+page.svelte -->
<script lang="ts">
  import { navigate, link } from 'svelte-routing';
  import { onMount } from 'svelte';

  let currentDateTime: string;

  function handleLogout() {
    window.location.href = '/project2/login'; 
  }

  onMount(() => {
    const updateDateTime = () => {
      const now = new Date();
      const options: Intl.DateTimeFormatOptions = {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        hour: 'numeric',
        minute: 'numeric',
        second: 'numeric',
        hour12: true,
      };
      currentDateTime = now.toLocaleString('en-US', options).replace(',', ' at'); // Format the date and time
    };
    updateDateTime();
    const interval = setInterval(updateDateTime, 1000); // Update every second
    return () => clearInterval(interval); // Cleanup on component destroy
  });
</script>

<nav class="bg-green-500 p-4 flex flex-col md:flex-row justify-between items-center">
  <div class="text-white text-lg font-bold">
    EVENT SCHEDULER
  </div>
  <div class="date-time-container text-white text-lg mt-2 md:mt-0 flex-1 text-center">
    {currentDateTime}
  </div>
  <button class="text-white font-semibold mt-2 md:mt-0 logout-button md:ml-auto">
    <i class="fas fa-sign-out-alt"></i>
  </button>
</nav>

<style>
  @media (max-width: 750px) {
    .date-time-container {
      display: none;
    }
    nav {
      flex-direction: row;
      justify-content: space-between;
      align-items: center;
      padding: 10px;
    }
    .logout-button {
      margin-left: auto;
      margin-top: 0;
    }
  }
</style>
