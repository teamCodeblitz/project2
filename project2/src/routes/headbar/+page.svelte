<!-- src/routes/headbar/+page.svelte -->
<script lang="ts">
  import { navigate, link } from 'svelte-routing';
  import { onMount } from 'svelte';

  let currentDateTime: string;

  function handleLogout() {
    navigate('/login'); // Navigate to the login page
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
  <div class="date-time-container text-white text-lg mt-2 md:mt-0">
    {currentDateTime}
  </div>
  <button class="text-white font-semibold mt-2 md:mt-0 logout-button" on:click={handleLogout}>
    <i class="fas fa-sign-out-alt"></i>
  </button>
</nav>

<style>
  @media (max-width: 648px) {
    .logout-button {
      margin-left: auto;
    }
    .date-time-container {
      display: flex;
      align-items: center;
      justify-content: center;
      margin-top: 10px;
    }
  }
</style>


