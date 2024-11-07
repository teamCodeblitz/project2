<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  let name = '';
  let contactNumber = '';
  let eventName = '';
  let startTime = '';
  let endTime = '';

  let errors = {
    name: '',
    contactNumber: '',
    eventName: '',
    startTime: '',
    endTime: ''
  };

  let isPopupOpen = true;

  function closePopup() {
    isPopupOpen = false;
  }

  function validateInputs() {
    errors = {
      name: name ? '' : 'THIS IS REQUIRED',
      contactNumber: contactNumber ? '' : 'THIS IS REQUIRED',
      eventName: eventName ? '' : 'THIS IS REQUIRED',
      startTime: startTime ? '' : 'THIS IS REQUIRED',
      endTime: endTime ? '' : 'THIS IS REQUIRED'
    };

    console.log('Validation Errors:', errors);
    return Object.values(errors).every(error => error === '');
  }

  function scheduleEvent() {
    if (!validateInputs()) {
      errors = { ...errors };
      console.log('Errors after validation:', errors);
      return;
    }

    const eventData = {
      contactNumber,
      name,
      eventName,
      startTime,
      endTime,
    };
    dispatch('schedule', eventData);

    console.log('Event Data:', eventData);

    const storedEvents = localStorage.getItem('eventData');
    let events = [];

    if (storedEvents) {
      try {
        events = JSON.parse(storedEvents);
        if (!Array.isArray(events)) {
          console.error('Stored data is not an array:', events);
          events = [];
        }
      } catch (error) {
        console.error('Error parsing stored events:', error);
      }
    }

    events.push(eventData);
    localStorage.setItem('eventData', JSON.stringify(events));

    // Reset form fields
    name = '';
    contactNumber = '';
    eventName = '';
    startTime = '';
    endTime = '';
    
    closePopup(); // Close the popup
  }

  function handleContactNumberInput(e: Event) {
    const target = e.target as HTMLInputElement;
    contactNumber = target.value.replace(/[^0-9]/g, '');
  }
</script>

<h2 class="text-2xl font-bold mb-4">Add Event</h2>
<form class="space-y-4">
  <div>
    <label for="name" class="block text-sm font-medium text-gray-700">Email</label>
    <input id="name" type="text" bind:value={name} class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring focus:ring-blue-500" placeholder="Enter your email" required />
    <p class="text-red-500">{errors.name}</p>
  </div>
  
  <div>
    <label for="contactNumber" class="block text-sm font-medium text-gray-700">Contact Number</label>
    <input id="contactNumber" type="text" bind:value={contactNumber} 
           class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring focus:ring-blue-500" 
           placeholder="Enter your contact number" required 
           on:input={handleContactNumberInput} />
    <p class="text-red-500">{errors.contactNumber}</p>
  </div>
  
  <div>
    <label for="eventName" class="block text-sm font-medium text-gray-700">Event Name</label>
    <input id="eventName" type="text" bind:value={eventName} class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring focus:ring-blue-500" placeholder="Enter your event name" required />
    <p class="text-red-500">{errors.eventName}</p>
  </div>
  
  <div class="grid grid-cols-2 gap-4">
    <div>
      <label for="startTime" class="block text-sm font-medium text-gray-700">Start Time</label>
      <input id="startTime" type="datetime-local" bind:value={startTime} 
             min={new Date().toISOString().slice(0, 16)} 
             class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring focus:ring-blue-500" required />
      <p class="text-red-500">{errors.startTime}</p>
    </div>
    <div>
      <label for="endTime" class="block text-sm font-medium text-gray-700">End Time</label>
      <input id="endTime" type="datetime-local" bind:value={endTime} 
             min={new Date().toISOString().slice(0, 16)} 
             class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring focus:ring-blue-500" required />
      <p class="text-red-500">{errors.endTime}</p>
    </div>
  </div>
  
  <button type="button" on:click={scheduleEvent} 
          class="w-full bg-blue-600 text-white py-2 rounded-md hover:bg-blue-700" 
          disabled={!name || !contactNumber || !eventName || !startTime || !endTime}>
    Schedule Now
  </button>
</form>

<style>
  @media (max-width: 750px) {
    form {
      width: 100%;
    }
  }
</style>