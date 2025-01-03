<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  let name = '';
  let contactNumber = '';
  let eventName = '';
  let startTime = '';
  let endTime = '';
  let location = '';

  let errors = {
    name: '',
    contactNumber: '',
    eventName: '',
    startTime: '',
    endTime: '',
    emailFormat: '',
    location: ''
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
      startTime: validateDate(startTime),
      endTime: validateDate(endTime),
      emailFormat: errors.emailFormat,
      location: location ? '' : 'THIS IS REQUIRED'
    };

    console.log('Validation Errors:', errors);
    return Object.values(errors).every(error => error === '');
  }

  function validateDate(dateString: string) {
    const date = new Date(dateString);
    if (date.getFullYear() < 2024) {
        return 'Year not valid'; // Error for year below 2024
    }
    return ''; // Clear error if valid
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
      location
    };

    // Check for duplicate startTime in local storage
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

    // Check for duplicate startTime
    const isDuplicate = events.some((event: { startTime: string }) => event.startTime === startTime);
    if (isDuplicate) {
        errors.startTime = 'That date is already reserved'; // Set error for duplicate date
        console.log('Duplicate start time error:', errors.startTime);
        return;
    }

    // Proceed to store the event if no duplicates
    events.push(eventData);
    localStorage.setItem('eventData', JSON.stringify(events));

    // Reset form fields
    name = '';
    contactNumber = '';
    eventName = '';
    startTime = '';
    endTime = '';
    location = '';
    
    closePopup(); // Close the popup
    window.location.reload(); // Reload the page
  }

  function handleContactNumberInput(e: Event) {
    const target = e.target as HTMLInputElement;
    const value = target.value.replace(/[^0-9]/g, ''); // Remove non-numeric characters

    // Validate the contact number
    if (target.value.match(/[a-zA-Z]/)) {
        errors.contactNumber = 'This must be numbers'; // Error for letters
    } else if (value.length > 11) {
        errors.contactNumber = 'This must only be 11 numbers'; // Error for more than 11 digits
    } else if (!value.startsWith('09') && value.length > 0) {
        errors.contactNumber = 'It must start at 09'; // Error for not starting with 09
    } else {
        errors.contactNumber = ''; // Clear error if valid
    }

    contactNumber = value; // Update the contact number
  }

  function validateEmail() {
    errors.emailFormat = name && !name.endsWith('@gmail.com') ? 'The format must be youremail@gmail.com' : '';
  }
</script>

<h2 class="text-2xl font-bold mb-4">Add Event</h2>
<form class="space-y-4">
  <div>
    <label for="name" class="block text-sm font-medium text-gray-700">Email</label>
    <input id="name" type="text" bind:value={name} class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring focus:ring-blue-500" placeholder="Enter your email" required on:input={validateEmail} />
    <p class="text-red-500">{errors.emailFormat}</p>
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
  
  <div>
    <label for="location" class="block text-sm font-medium text-gray-700">Location</label>
    <div class="flex items-center">
      <input id="location" type="text" bind:value={location} 
             class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring focus:ring-blue-500" 
             placeholder="Enter event location" required />
      <button type="button" on:click={() => window.open(`https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(location)}`, '_blank')} class="ml-2">
        <i class="fas fa-globe"></i> <!-- Font Awesome Globe Icon -->
      </button>
    </div>
    <p class="text-red-500">{errors.location}</p>
  </div>
  
  <button type="button" on:click={scheduleEvent} 
          class="w-full bg-blue-600 text-white py-2 rounded-md hover:bg-blue-700" 
          disabled={!name || !contactNumber || !eventName || !startTime || !endTime || !location}>
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