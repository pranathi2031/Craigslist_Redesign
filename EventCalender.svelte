<script>
  import { onMount } from "svelte";

  let selectedDate = new Date(); // Current date
  let selectedEvent = null; // Holds the currently selected event for the modal
  let events = {
    "2024-11-18": [
      {
        title: "Musical Event",
        image:"https://static01.nyt.com/images/2019/06/14/arts/14listings-classical2/14listings-classical2-videoSixteenByNineJumbo1600.jpg",
        description: "The showcase of different instruments ending with a concert",
        location: "Musical Arena",
        time: "7:00 PM- 12:00 AM"
      },
      {
        title: "Lunch with Voyagers",
        image: "https://th.bing.com/th/id/OIP.sGlebaAqBzeY8sOheDjuIgHaE8?w=241&h=180&c=7&r=0&o=5&dpr=1.3&pid=1.7",
        description: "Lunch and Learn Event.",
        location: "The Grill Restaurant",
        time: "1:00 PM - 2:30 PM"
      }
    ],
    "2024-11-19": [
      {
        title: "Workshop: AI in 2024",
        image: "https://th.bing.com/th/id/OIP.B85lJjbdDyp5MyiGRhLl8gHaED?w=309&h=180&c=7&r=0&o=5&dpr=1.3&pid=1.7",
        description: "Learn about the latest advancements in AI.",
        location: "Tech Auditorium",
        time: "9:00 AM - 3:00 PM"
      }
    ],
    "2024-11-30": [
      {
        title: "Workshop: NLP in 2024",
        image: "https://d2mk45aasx86xg.cloudfront.net/Natural_Language_Processing_in_action_11zon_be0e4fa306.webp",
        description: "Learn about the latest advancements in NLP.",
        location: "Tech Auditorium",
        time: "9:00 AM - 3:00 PM"
      }
    ]
  };

  let days = [];
  let currentMonth = selectedDate.getMonth();
  let currentYear = selectedDate.getFullYear();

  const getDaysInMonth = (month, year) => {
    let date = new Date(year, month, 1);
    let days = [];
    while (date.getMonth() === month) {
      days.push(new Date(date));
      date.setDate(date.getDate() + 1);
    }
    return days;
  };

  const changeMonth = (direction) => {
    currentMonth += direction;
    if (currentMonth < 0) {
      currentMonth = 11;
      currentYear -= 1;
    } else if (currentMonth > 11) {
      currentMonth = 0;
      currentYear += 1;
    }
    days = getDaysInMonth(currentMonth, currentYear);
  };

  const selectDate = (date) => {
    selectedDate = date;
  };

  const openEventDetails = (event) => {
    selectedEvent = event;
  };

  const closeModal = () => {
    selectedEvent = null;
  };

  onMount(() => {
    days = getDaysInMonth(currentMonth, currentYear);
  });
</script>

<style>
  /* General Styling */
  .calendar-container {
    display: flex;
    gap: 20px;
  }

  .calendar {
    width: 500px;
    border: 1px solid #ddd;
    border-radius: 10px;
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
    background: linear-gradient(to bottom, #fff, #f9f9f9);
    overflow: hidden;
    
    margin-bottom:20px;
    margin-left:20px;
  }

  .calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    background:linear-gradient(125deg, rgb(85, 26, 139), rgb(206 68 164));;
    -webkit-background-clip: border-box; /* For Chrome, Safari */
    background-clip: border-box; /* For Firefox */
    color: transparent;
  
    color: white;
    font-weight: bold;
  }

  .calendar-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
    padding: 10px;
    background: #fff;
  }

  .day {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    text-align: center;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.3s ease-in-out;
  }

  .day:hover {
    background: #f0f8ff;
    transform: scale(1.05);
  }

  .day.selected {
    background-color: rgb(206 68 164);
    color: white;
  }

  .events {
    flex: 1;
    border: 1px solid #ddd;
    border-radius: 10px;
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
    padding: 10px;
    background: #f9f9f9;
    margin-bottom: 20px;
  }

  .events h3 {
    margin-top: 0;
    text-align: center;
    color: black;
    
    
  }

  .event {
    display: flex;
    gap: 15px;
    margin-bottom: 15px;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 8px;
    background: #fff;
    transition: all 0.3s ease-in-out;
    cursor: pointer;
  }

  .event:hover {
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
    transform: translateY(-3px);
  }

  .event img {
    width: 80px;
    height: 80px;
    border-radius: 10px;
    object-fit: cover;
  }

  .event-details {
    flex: 1;
  }

  .event-details h4 {
    margin: 0 0 5px;
    color: #333;
  }

  .event-details p {
    margin: 0;
    font-size: 14px;
    color: #555;
  }

  .no-events {
    text-align: center;
    color: #999;
    margin-top: 20px;
  }

  /* Modal Styling */
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
  }

  .modal {
    background: white;
    border-radius: 10px;
    padding: 30px;
    width: 400px;
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
    text-align: center;
    position: relative;
  }

  .modal img {
    width: 100%;
    border-radius: 10px;
    margin-bottom: 15px;
  }

  .modal h4 {
    margin: 10px 0;
    color:black;
  }

  .modal p {
    margin: 5px 0;
    font-size: 15px;
    color: #555;
  }

  .modal-close {
    position: absolute;
    bottom:10px;
    right: 210px;
    background-color:#781097;
    border: 1px solid #781097;
    border-radius: 5px;
    font-size: 14px;
    cursor: pointer;
    color:white;
    font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
    font-weight: bold;
  }

  .modal-close:hover {
    
    transform: scale(1.15);
  }
</style>

<div class="calendar-container">
  <!-- Calendar Section -->
  <div class="calendar">
    <div class="calendar-header">
      <button on:click={() => changeMonth(-1)}>❮</button>
      <div>
        {new Date(currentYear, currentMonth).toLocaleString("default", { month: "long" })} {currentYear}
      </div>
      <button on:click={() => changeMonth(1)}>❯</button>
    </div>

    <div class="calendar-days">
      {#each days as day}
        <div
          class="day {selectedDate.toDateString() === day.toDateString() ? 'selected' : ''}"
          on:click={() => selectDate(day)}>
          {day.getDate()}
          <!-- Check if events are available for this day -->
          {#if events[day.toISOString().split("T")[0]]}
            <span class="star">★</span> <!-- Star icon -->
          {/if}
        </div>
      {/each}
    </div>
  </div>

  <!-- Events Section -->
  <div class="events">
    <h3>Events on {selectedDate.toDateString().split(' ').slice(1).join(' ')}</h3>
    {#if events[selectedDate.toISOString().split("T")[0]]}
      {#each events[selectedDate.toISOString().split("T")[0]] as event}
        <div class="event" on:click={() => openEventDetails(event)}>
          <img src={event.image} alt={event.title} />
          <div class="event-details">
            <h4>{event.title}</h4>
            <p><strong>Time:</strong> {event.time}</p>
            <p><strong>Location:</strong> {event.location}</p>
          </div>
        </div>
      {/each}
    {:else}
      <p class="no-events">No events for this day.</p>
    {/if}
  </div>
</div>

<!-- Modal Section -->
{#if selectedEvent}
  <div class="modal-overlay" on:click={closeModal}>
    <div class="modal" on:click|stopPropagation>
      <button class="modal-close" on:click={closeModal}>Close</button>
      <img src={selectedEvent.image} alt={selectedEvent.title} />
      <h4>{selectedEvent.title}</h4>
      <p><strong>Time:</strong> {selectedEvent.time}</p>
      <p><strong>Location:</strong> {selectedEvent.location}</p>
      <p>{selectedEvent.description}</p>
    </div>
  </div>
{/if}
