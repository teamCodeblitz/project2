<script lang="ts">
	import Headbar from '../headbar/+page.svelte'; // Importing the headbar component\
	import CreateEvent from '../create-event/+page.svelte';
	import { onMount } from 'svelte';
	import '@fortawesome/fontawesome-free/css/all.css';

	interface Event {
		contactNumber: string;
		name: string;
		eventName: string;
		startTime: string;
		endTime: string;
		location: string;
	}

	let showPopup = false; // State to control the popup visibility
	let events: Event[] = []; // Initialize as an empty array
	let selectedEvent: Event | null = null; // State to hold the event being edited
	let confirmDeleteEvent: Event | null = null; // State to hold the event to be deleted
	let showAddEventPopup = false; // State to control the add event popup visibility
	let currentPage = 1; // State to track the current page
	const eventsPerPage = 18; // Number of events to display per page

	let windowWidth = 0;

	onMount(() => {
		// Set initial window width
		windowWidth = window.innerWidth;

		const handleResize = () => {
			windowWidth = window.innerWidth; // Update window width on resize
		};

		window.addEventListener('resize', handleResize);
		return () => {
			window.removeEventListener('resize', handleResize); // Clean up on component unmount
		};
	});

	function addEvent(event: CustomEvent<Event>) {
		events = [...events, event.detail]; // Add new event to the events array
		showPopup = false; // Close the popup after scheduling
	}

	function togglePopup() {
		showPopup = !showPopup; // Toggle the popup visibility
	}

	function toggleAddEventPopup() {
		showAddEventPopup = !showAddEventPopup; // Toggle the add event popup visibility
	}

	function confirmDelete(event: Event) {
		confirmDeleteEvent = event; // Set the event to be deleted
		showAddEventPopup = false; // Close the add event popup if open
		showPopup = true; // Show the confirmation popup
	}

	function deleteConfirmed() {
		if (confirmDeleteEvent) {
			events = events.filter((e) => e !== confirmDeleteEvent); // Remove the event from the events array
			localStorage.setItem('eventData', JSON.stringify(events)); // Update local storage
			confirmDeleteEvent = null; // Reset the confirmation state
		}
		showPopup = false; // Close the confirmation popup
	}

	function cancelDelete() {
		confirmDeleteEvent = null; // Reset the confirmation state
		showPopup = false; // Close the confirmation popup
	}

	function formatDateTime(dateTime: string): string {
		const options: Intl.DateTimeFormatOptions = {
			year: 'numeric',
			month: 'long',
			day: 'numeric',
			hour: 'numeric',
			minute: 'numeric',
			hour12: true
		};
		const currentYear = new Date().getFullYear(); // Get the current year
		const eventYear = new Date(dateTime).getFullYear(); // Get the event year
		if (eventYear < currentYear) {
			return 'Invalid date'; // Return a message for invalid dates
		}
		return new Date(dateTime).toLocaleString('en-US', options);
	}

	function openEditPopup(event: Event) {
		selectedEvent = event; // Set the selected event for editing
		showPopup = true; // Show the popup
	}

	function nextPage() {
		if (currentPage * eventsPerPage < events.length) {
			currentPage++; // Increment the current page
		}
	}

	function previousPage() {
		if (currentPage > 1) {
			currentPage--; // Decrement the current page
		}
	}

	// Load events from local storage on component mount
	onMount(() => {
		const storedEvents = localStorage.getItem('eventData');
		if (storedEvents) {
			try {
				const parsedEvents = JSON.parse(storedEvents);
				// Ensure parsedEvents is an array
				if (Array.isArray(parsedEvents)) {
					events = parsedEvents.sort(
						(a, b) => new Date(b.startTime).getTime() - new Date(a.startTime).getTime()
					); // Sort events by start time (newest first)
				} else {
					console.error('Stored data is not an array:', parsedEvents);
				}
			} catch (error) {
				console.error('Error parsing stored events:', error);
			}
		}
	});
</script>

<Headbar />
<div class="flex-box flex h-[calc(100vh-64px)] flex-col overflow-hidden p-6">
	<button
		class="mb-4 rounded-lg bg-blue-600 px-5 py-3 text-white transition duration-200 hover:bg-blue-700"
		on:click={toggleAddEventPopup}>Add Event</button
	>

	{#if showAddEventPopup}
		<div class="popup fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-50">
			<div class="relative w-full max-w-md rounded-lg bg-white p-6 shadow-lg">
				<button
					on:click={toggleAddEventPopup}
					class="absolute right-2 top-2 text-gray-500 hover:text-gray-800"
				>
					&times;
				</button>
				<CreateEvent on:schedule={addEvent} />
			</div>
		</div>
	{/if}

	{#if confirmDeleteEvent}
		<div class="delete-popup fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-50">
			<div class="relative w-full max-w-md rounded-lg bg-white p-6 shadow-lg">
				<h2 class="delete-text text-center text-lg font-bold">Are you sure you want to delete it?</h2>
				<div class="mt-4 flex justify-center">
					<button
						on:click={deleteConfirmed}
						class="mr-2 rounded-lg bg-red-600 px-4 py-2 text-white transition duration-200 hover:bg-red-700"
						>Yes</button
					>
					<button
						on:click={cancelDelete}
						class="rounded-lg bg-gray-300 px-4 py-2 text-gray-700 transition duration-200 hover:bg-gray-400"
						>No</button
					>
				</div>
			</div>
		</div>
	{/if}

	<div class="overflow-x-auto">
		<div>
			{#if events.length === 0}
				<p class="text-center text-gray-500">No events have been scheduled yet.</p>
			{:else if windowWidth < 450}
				<div class="card-container grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
					{#each events.slice((currentPage - 1) * eventsPerPage, currentPage * eventsPerPage) as event}
						<div class="card border border-gray-300 rounded-lg p-6 shadow-md transition-shadow duration-200 bg-white hover:shadow-lg hover:scale-105">
							<h3 class="font-bold text-lg mb-2">{event.eventName}</h3>
							<p class="text-gray-700"><strong>Contact:</strong> {event.contactNumber}</p>
							<p class="text-gray-700"><strong>Start Time:</strong> {formatDateTime(event.startTime)}</p>
							<p class="text-gray-700"><strong>End Time:</strong> {formatDateTime(event.endTime)}</p>
							<p class="text-gray-700"><strong>Location:</strong> {event.location}</p>
							<button class="mt-4 text-red-600 hover:text-red-700 transition duration-200 transform hover:scale-105" on:click={() => confirmDelete(event)}>
								<i class="fas fa-trash"></i> Delete
							</button>
						</div>
					{/each}
				</div>
			{:else}
				<table class="min-w-full divide-y divide-gray-200">
					<thead class="bg-gray-50">
						<tr>
							<th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Event Name</th>
							<th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Contact</th>
							<th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Start Time</th>
							<th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">End Time</th>
							<th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Location</th>
							<th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
						</tr>
					</thead>
					<tbody class="bg-white divide-y divide-gray-200">
						{#each events as event, index}
							<tr class={index % 2 === 0 ? 'bg-gray-50 hover:bg-gray-100' : 'bg-white hover:bg-gray-100'}>
								<td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">{event.eventName}</td>
								<td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">{event.contactNumber}</td>
								<td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">{formatDateTime(event.startTime)}</td>
								<td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">{formatDateTime(event.endTime)}</td>
								<td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">{event.location}</td>
								<td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
									<button class="text-red-600 hover:text-red-700 transition duration-200 transform hover:scale-105" on:click={() => confirmDelete(event)}>
										<i class="fas fa-trash"></i> Delete
									</button>
								</td>
							</tr>
						{/each}
					</tbody>
				</table>
			{/if}
		</div>
	</div>
</div>

<!-- Fixed Navigation Buttons at the very end -->
<div class="fixed bottom-0 left-0 right-0 flex justify-between bg-white p-4 shadow-md">
	<button
		class="rounded-lg bg-gray-300 px-4 py-2 text-gray-700 transition duration-200 hover:bg-gray-400"
		on:click={previousPage}
		disabled={currentPage === 1}
	>
		Back
	</button>
	<p class="page-info">Page {currentPage} of {Math.max(1, Math.ceil(events.length / eventsPerPage))}</p>
	<button
		class="rounded-lg bg-blue-600 px-4 py-2 text-white transition duration-200 hover:bg-blue-700"
		on:click={nextPage}
		disabled={currentPage * eventsPerPage >= events.length}
	>
		Next
	</button>
</div>

<style>
	@media (max-width: 750px) {
		.popup {
			width: 100%;
			padding: 20px;
		}

		.fixed {
			flex-direction: row;
			justify-content: space-between;
			align-items: center;
			box-shadow: 0 -2px 5px rgba(0, 0, 0, 0.2);
		}
		.fixed button {
			width: auto;
			margin-bottom: 0;
		}

		.overflow-x-auto {
			overflow-x: auto; 
			overflow-y: auto; 
			max-height: 533px;
		}
		table {
			width: 100%;
			font-size: 0.8rem;
			table-layout: auto; 
		}
		th, td {
			padding: 8px;
			word-wrap: break-word;
		}
		.page-info {
			flex-grow: 1;
			text-align: center;
			margin: 0;
		}
    .delete-popup{
      padding: 20px;
      text-align: center;
      font-size: small;
    }
    .delete-text{
      font-size: 1.0rem;
    }
	}

	@media (max-width: 450px) {
		/* Card style for mobile view */
		.overflow-x-auto {
			overflow-x: hidden; 
		}
		table {
			display: none; /* Hide the table */
		}
		.card-container {
			display: flex;
			flex-direction: column;
		}
		.card {
			background-color: white;
			box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
			margin-bottom: 10px;
		}
	}

	@media (min-width: 451px) {
		/* Table style for larger screens */
		.card {
			display: none; /* Hide the card */
		}
		table {
			width: 100%; /* Show the table */
			display: table; /* Ensure the table is displayed */
		}
	}
</style>
