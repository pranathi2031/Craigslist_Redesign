<script>
  import Footer from './Footer.svelte';
  import EventCalender from './EventCalender.svelte';
  import { Router, Route, navigate } from 'svelte-routing';
  import { writable } from 'svelte/store';
  import { onMount } from 'svelte';
  import data from './Catergories.json';
    import Discussion from './discussion.svelte';

  let location = "Cincinnati";
  let addPost = false;
  let latestEntries = [
    { title: 'Aquatic Instructor (Full Time)', pay:"$15/hr",employment:"Full Time",experience:"Senior Level", url: '/post1', imageUrl: 'https://images.craigslist.org/00H0H_9ewdwFtMufs_07V063_600x450.jpg',location:'Dayton',date:'Nov 10',liked:'false'},
    { title: 'Experienced Service and Parts Manager',pay:"$30/hr",employment:"Full Time",experience:"Senior Level" , url: '/post1', imageUrl: 'https://images.craigslist.org/00m0m_95zuOYe5HH5_0g707R_600x450.jpg',location:'Illinois',date:'Nov 15',liked:'false'},
    { title: 'Project Manager(Part time)', pay:"$30/hr",employment:"Part Time",experience:"Senior Level", url: '/post1', imageUrl: 'https://th.bing.com/th/id/OIP.u4EBes6Muu2fy7iM8igMugHaFX?w=244&h=180&c=7&r=0&o=5&dpr=1.3&pid=1.7',location:'Columbus',date:'Nov 17',liked:'false'},
    { title: 'Apartment Leasing Consultant', pay:"$17/hr",employment:"Full Time",experience:"Some prior experience", url: '/post1', imageUrl: 'https://lewiscareers.com/wp-content/uploads/2020/11/Leasing-Agent.jpg',location:'Cleveland',date:'Today',liked:'false'},
    { title: 'Deliver with DoorDash',pay:"$20/hr",employment:"Part Time",experience:"Good driving skills", url: '/post1', imageUrl: 'https://th.bing.com/th/id/OIP.sykEUuxSMYBKw2IYyFdvrAHaEK?w=280&h=180&c=7&r=0&o=5&dpr=1.3&pid=1.7',location:'Cincinnati',date:'Nov 16',liked:'true'}
    
  ];
  
  let header;
  let isScrolling = false;
  let isHovered = false;
  let isOverflowing = false;

  // Function to check if the container is overflowing
  function checkOverflow() {
    if (scrollContainer) {
      // Check if the scrollable content is larger than the container
      isOverflowing = scrollContainer.scrollWidth > scrollContainer.clientWidth;
    }
  }

  // Call checkOverflow when the component mounts and when the window resizes

  onMount(() => {
    checkOverflow();
    window.addEventListener('resize', checkOverflow);  // Recheck on window resize
  });

  // Clean up on component destruction
  import { onDestroy } from 'svelte';
  onDestroy(() => {
    window.removeEventListener('resize', checkOverflow);
  });

  let scrollTimeout;
  export const headerHeight = writable(0);
  let selectedCategory = JSON.parse(localStorage.getItem("selectedCategory")) || null;
  let selectedSubcategory = JSON.parse(localStorage.getItem("selectedSubcategory")) || null;
  let isDropdownOpen = false;
  let hoveredCategory = null;


  function toggleLike(entry) {
    entry.liked = !entry.liked;
    console.log(entry.liked);
    latestEntries = [...latestEntries]; 
  }
  let scrollContainer;

  function scrollLeft() {
    scrollContainer.scrollBy({
      left: -200, // Adjust the value as needed
      behavior: 'smooth'
    });
  }

  function scrollRight() {
    scrollContainer.scrollBy({
      left: 200, // Adjust the value as needed
      behavior: 'smooth'
    });
  }

  // Function to delete an entry
  function deleteEntry(entry) {
    latestEntries = latestEntries.filter(e => e !== entry);
  }
  // Calculate and set header height
  const calculateHeaderHeight = () => {
    if (header) {
      headerHeight.set(header.offsetHeight);
    }
  };
  // Update breadcrumb and browser history
  
  function updateBreadcrumb(category, subcategory = null) {
    selectedCategory = category;
    selectedSubcategory = subcategory;

    // Save the selected category and subcategory to localStorage
    localStorage.setItem("selectedCategory", JSON.stringify(category));
    if (subcategory) {
      localStorage.setItem("selectedSubcategory", JSON.stringify(subcategory));
      navigate(subcategory.url); // Navigate to the subcategory's URL
      window.history.pushState(
        { category, subcategory },
        '',
        subcategory.url
      );
    } else {
      localStorage.removeItem("selectedSubcategory");
      navigate(category.url); // Navigate to the category's main page or home
      window.history.pushState({ category }, '', category.url);
    }
  }


  
  // Handle back navigation
  window.addEventListener('popstate', (event) => {
    if (event.state) {
      selectedCategory = event.state.category;
      selectedSubcategory = event.state.subcategory;
    } else {
      selectedCategory = null;
      selectedSubcategory = null;
    }
    discussion=false;
    search=false;
  });

  // Toggle dropdown visibility
  function toggleDropdown(category) {
    isDropdownOpen = true;
    selectedCategory = category;
  }

  function closeDropdown() {
    isDropdownOpen = false;
  }
  
  function showpost()
  {
    addPost = true;
  }
  const items = [
  { name: 'Coffee Shop', description: 'Great coffee and pastries', location: { lat: 40.7128, lon: -74.0060 }, city: 'New York' },
  { name: 'Pizza Place', description: 'Delicious pizza and pasta', location: { lat: 34.0522, lon: -118.2437 }, city: 'Los Angeles' },
  { name: 'Sushi Bar', description: 'Authentic Japanese sushi', location: { lat: 35.6895, lon: 139.6917 }, city: 'Tokyo' },
  // Add more items as needed...
];





// Main search function
function searchItems(queryText, location, radius = 60) {
  // Ensure items array exists
  if (!Array.isArray(items)) {
    throw new Error("items must be an array");
  }

  // Normalize the query text
  const lowerQuery = queryText?.toLowerCase() || "";
 console.log(lowerQuery);
  // If no query is provided, return an empty array
  if (!lowerQuery) {
    return [];
  }

  // Filter items based on text match and location match
  return items.filter(item => {
    // Safely handle potential null/undefined properties
    const itemName = item?.name?.toLowerCase() || "";
    const itemDescription = item?.description?.toLowerCase() || "";
    const itemCity = item?.city?.toLowerCase() || "";

    // Check if the query text matches name or description
    const matchesText =
      lowerQuery &&
      (itemName.includes(lowerQuery) || itemDescription.includes(lowerQuery));

    // Location matching
    let matchesLocation = true;
    if (location) {
      if (location.lat && location.lon) {
        // Lat/Lon-based location matching
        const itemLat = item?.location?.lat;
        const itemLon = item?.location?.lon;

        if (itemLat != null && itemLon != null) {
          const distance = haversine(location.lat, location.lon, itemLat, itemLon);
          matchesLocation = distance <= radius; // Within radius
        } else {
          matchesLocation = false; // No valid item location
        }
      } else if (typeof location === "string") {
        // City-based location matching
        matchesLocation = itemCity.includes(location.toLowerCase());
      }
    }

    // Return true if both text and location match
    return matchesText || matchesLocation;
  });
}

// Example Haversine formula function for calculating distances
function haversine(lat1, lon1, lat2, lon2) {
  const toRad = angle => (Math.PI / 180) * angle;
  const R = 6371; // Earth's radius in kilometers

  const dLat = toRad(lat2 - lat1);
  const dLon = toRad(lon2 - lon1);
  const a = 
    Math.sin(dLat / 2) * Math.sin(dLat / 2) +
    Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
    Math.sin(dLon / 2) * Math.sin(dLon / 2);

  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  return R * c; // Distance in kilometers
}

// Variables
let textQuery = '';
let locationQuery = '';
let searchResults = [];
let searchQuery = '';
let userLocation = null;
let search;
let focusedIndex = -1;
let isSelect = false;
let discussion;

// Function to update search results
function updateSearch() {
  const location = userLocation || locationQuery; 
  console.log(userLocation);// Use user location or city query
  searchResults = searchItems(textQuery, location, 10);
  console.log(searchResults);
  }

// Handle Enter key press
function handleKeyPress(event) {
  
    if(event.key === 'Enter'){
      handleSearch();
    }
 
  }

let suggestions = ["Apple", "Banana", "Cherry", "Date", "Eggplant", "Fig", "Grape", "Honeydew"];// Suggestions filtered based on input

// Handle search button click
function handleSearch() {

  search = true;
  discussion= false;
  
    const searchUrl = `/SearchResults?query=${encodeURIComponent(textQuery)}&location=${encodeURIComponent(locationQuery)}`;
    navigate(searchUrl); // Use `svelte-routing`'s `navigate`
    window.history.pushState({ textQuery, locationQuery }, '', searchUrl);
     // Hide suggestions after selection
    
    // You can also trigger a search or perform other actions here
    console.log(textQuery);
    updateSearch();
  
    
  
}


  




  

// Set user location (browser geolocation)
onMount(() => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(position => {
      userLocation = {
        lat: position.coords.latitude,
        lon: position.coords.longitude,
      };
      updateSearch(); // Update search with user location
    });
  }

 
});
onMount(() => {
  const urlParams = new URLSearchParams(window.location.search);
  textQuery = urlParams.get('query') || '';
  locationQuery = urlParams.get('location') || '';
  updateSearch();
});



  onMount(() => {
    calculateHeaderHeight();
    window.addEventListener('resize', calculateHeaderHeight);
    return () => window.removeEventListener('resize', calculateHeaderHeight);
  });

  // $: filteredNameSuggestions = nameSuggestions.filter(suggestion => 
  //   suggestion.toLowerCase().includes(textQuery.toLowerCase())
  // );


    let searchContainer;
   
  
 
  function redirectTo(link) {
    window.location.href = link; // Redirects to the specified URL
  }
  function setDiscussion() {
    discussion = true;
    navigate('/Discussions');
    console.log(discussion);
    console.log(search);
    search= false;
    
    
  }
  console.log(search);
  
</script>

<Router>
  <main>
    <div class="container">
      <div class="sticky">
        <header bind:this={header}>
          <p class="header-logo"><i class="fas fa-peace"></i> Craigslist</p>
          <div class="search-container" bind:this={searchContainer}>
            <!-- Search Query Input -->
            <div style="flex: 1; display: flex; align-items: center;">
              <i class="fa fa-search" style="margin: 0 10px;color: #777;;"></i>
              <input
                type="text"
                bind:value={textQuery}
                class="search-input"
                placeholder="Search Craigslist"
                on:input={this}
                on:keydown={(event) => handleKeyPress(event)}
              />
            </div>
          
            <!-- Location Input -->
            <div style="flex: 1; display: flex; align-items: center; border-left: 1px solid #ccc;">
              <i class="fa fa-map-marker-alt" style="margin: 0 10px; color: #555;"></i>
              <input
                type="text"
                bind:value={locationQuery}
                class="search-input location-query"
                placeholder="Cincinnati"
                on:keydown={(event) => handleKeyPress(event)}
              />
            </div>
          
            <!-- Search Button -->
            <button class="search-icon">
              <i class="fa fa-search"></i>
            </button>
          </div>
          <div class="header-actions"> 
            <button class="icon-button">Post</button>
            <button class="icon-button">Sign In</button>
            <button class="icon-button">Register</button>
            <button class="icon-button discussion-btn" on:click={setDiscussion}>Discussion</button>
          </div>
        </header>
        <div class="breadcrumbs-box">
          <div class="breadcrumbs">
            {#if !selectedCategory && !selectedSubcategory}
              <span class="breadcrumb" on:click={() => { 
                selectedCategory = null; selectedSubcategory = null;search=false;discussion=false; textQuery="";locationQuery="";
                localStorage.removeItem("selectedCategory"); localStorage.removeItem("selectedSubcategory"); 
                navigate("/"); 
                window.history.pushState({}, '', '/'); // Ensure home URL is pushed to history
              }}>
                Home</span>
                {#if search && selectedCategory === null && discussion === false}
                <span class="breadcrumb-separator">›</span>
               <span class="breadcrumb active" >Search Results</span>
              {/if}
              {#if discussion && selectedCategory === null}
                <span class="breadcrumb-separator">›</span>
               <span class="breadcrumb active" >Discussion</span>
              {/if}
              
            {:else if selectedCategory}
              <span class="breadcrumb" on:click={() => { 
                selectedCategory = null; selectedSubcategory = null; 
                localStorage.removeItem("selectedCategory"); localStorage.removeItem("selectedSubcategory"); 
                navigate("/"); 
                window.history.pushState({}, '', '/'); 
                search=false; discussion=false; textQuery="";locationQuery="";// Ensure home URL is pushed to history
              }}>
                Home
              </span>
              
              <span class="breadcrumb-separator">›</span>
              {#if selectedSubcategory && !search && !discussion}
                <span class="breadcrumb active" on:click={() => { 
                  selectedSubcategory = null; 
                  localStorage.removeItem("selectedSubcategory"); 
                  const firstSubcategoryUrl = selectedCategory.url || "/"; 
                  navigate(firstSubcategoryUrl); 
                  window.history.pushState({ category: selectedCategory, subcategory: null }, '', firstSubcategoryUrl); // Update history
                }}>
                  {selectedCategory.name}
                </span>
                <span class="breadcrumb-separator">›</span>
                <span class="breadcrumb active">
                  {selectedSubcategory.name}
                </span>
              {:else if search && (selectedCategory || selectedSubcategory) && !discussion}
              <span class="breadcrumb active" >Search Results</span>
              {:else if discussion && (selectedCategory || selectedSubcategory) && !search}
              <span class="breadcrumb active" >Discussion</span>
              
              {:else}
                <span class="breadcrumb active">{selectedCategory.name}</span>
              {/if}
            {/if}
          </div>
          
        </div>
        
        {#if !search && !discussion}
        <div class="navbar">
          {#each data.Categories as category}
            <div
              class="category-card"
              on:click={() => updateBreadcrumb(category)}
              on:mouseenter={() => (hoveredCategory = category)}
              on:mouseleave={() => (hoveredCategory = null)}
              style:selected={selectedCategory === category ? 'background-color: yellow; color: white;' : ''}>
              <p><i class={category.icon}></i> {category.name}</p>
              {#if category.subcategories}
              <div class="dropdown">
                  {#each category.subcategories as subcategory}
                    <div class="subcategory-item"
                      on:click={(event) => {
                        event.stopPropagation();
                        updateBreadcrumb(category, subcategory);
                      }}
                    >
                      {subcategory.name}
                    </div>
                  {/each}
              </div>
              {/if}
            </div>
          
          {/each}
        </div>
        {/if}
        
      </div>
      {#if hoveredCategory}
        <div class="overlay" on:click={() => (hoveredCategory = null)}></div>
      {/if}
 
        
      
      <Route path="/" let:params>
        <div class="content">
          <div class="latest-entries">
            <p class="heading">Latest Job Postings</p> 
                <div class="card-container">
                  {#each latestEntries as entry}
                    <div class="card">
                      <img src={entry.imageUrl} alt={entry.title} class="card-image" />
                      <a href={entry.url} class="card-title">{entry.title}</a>
                      <p class="card-description"><b>Employment Type:</b> {entry.employment}</p>
                      <p class="card-description"><b>Compensation:</b> {entry.pay}</p>
                      <p class="card-description"><b>Required Experience:</b> {entry.experience}</p>
              
                      <div class="card-footer">
                        <div class="footer-item">
                          <i class="fas fa-map-marker-alt"></i>
                          <span>{entry.location}</span>
                        </div>
                        <div class="footer-item">
                          <span>Posted on:</span>
                          <span>{entry.date}</span>
                        </div>
                      </div>
              
                      <div class="card-actions">
                        <button class="like-button" on:click={() => toggleLike(entry)}>
                          {#if entry.liked}
                          <i class= 'fa fa-bookmark'></i>
                          {:else}
                          <i style="color:#781097;"class= 'fa-regular fa-bookmark dislike-button'></i>
                          {/if}
                        </button>
                        <button class="delete-button" on:click={() => deleteEntry(entry)}>
                          <i class="fa fa-trash"></i>
                        </button>
                      </div>
                    </div>
                  {/each}
                </div>
           </div>
          </div><br><br>
          <p class="heading1">Events Calender</p>
          <EventCalender />
          
          
        
      </Route>

      

      <Route path="/SearchResults" let:params>
        <p class="results">Search Results</p>
        <div class="results">
          {#if searchResults.length>0}
          {#each searchResults as item}
        
            <div class="item">
              <h3>{item.name}</h3>
              <p>{item.description}</p>
              <p>Location: {item.city}</p>
            </div>

          {/each}
          {:else}
          <p>No results found</p>
          {/if}
        </div>
        
        
      </Route>
      <Route path="/Discussions">
       <Discussion/>
      </Route>
      
    </div>
    <Footer />
  </main>
</Router>
<style>
  
  .container {
    display: flex;
    flex-direction: column;
    height: auto;
    background-color: #F8F9F9;
  }
  .heading1{
    margin-left:20px;
    font-size: 22px;
    color:black;
    font-weight: bold;
  
  }
  .results{
    margin-left:20px;
  }

  header {
    display: flex;
    align-items: center;
    padding: 10px 30px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    position: sticky;
    top: 0;
    z-index: 2000;
    background:linear-gradient(125deg, rgb(85, 26, 139), rgb(206 68 164));;
    -webkit-background-clip: border-box; /* For Chrome, Safari */
    background-clip: border-box; /* For Firefox */
    color: transparent;
  }
  .sticky{
    position:sticky;
    top:0;
    z-index:1000;
  }

  .heading{
    font-size: 22px;
    color:black;
    font-weight: bold;
  }
  .header-logo {
    font-size: 28px;
    font-weight: bold;
    
    color:white;
     /* Makes the text color transparent so the gradient shows */
}


  .header-actions {
    margin-left: auto;
    display: flex;
    gap: 10px;
  }

  .icon-button {
    background-color: white;
    border: none;
    border-radius: 5px;
    padding: 8px 15px;
    cursor: pointer;
    font-size: 16px;
    font-weight: bold;
  }

  .icon-button:hover {
    transform: scale(1.15);
    background-color:rgb(230, 228, 228);
    
  }

  .discussion-btn {
    background-color:white;
  }

  .content {
    display: flex;
    flex-direction: column;
    padding:18px;
  }

  .search-container {
    display: flex;
    align-items: center;
    border: 1px solid #ccc;
    border-radius: 5px;
    overflow: hidden;
    width: 600px;
    background-color: #fff;
  }


  /* .search-input {
    border: 1px solid #DDD;
    border-radius: 8px;
    outline: none;
    flex: 1;
    padding: 10px;
    font-size: 16px;
    margin-left:20px;
  } */

  /* .search-icon {
    
    border: none;
    cursor: pointer;
    font-size: 20px;
    padding-right: 20px;
    background-color: none;
    background:none;
   } 
   */
  .search-container {
    display: flex;
    align-items: center;
    margin-left:70px;
  }

  .search-input {
    border: none;
    outline: none;
    padding: 10px;
    font-size: 16px;
    flex: 1;
  }

  
  .suggestions-list {
    list-style: none;
    margin: 0;
    padding: 0;
    position: absolute;
    background-color: #fff;
    border: 1px solid #ccc;
    width: 100%;
    z-index: 10;
  }

  .suggestions-list li {
    padding: 8px 10px;
    cursor: pointer;
  }

  .suggestions-list li.focused {
    background-color: #f0f0f0;
  }

  /* Search Button */
  .search-icon {
    background-color: grey;
    color: black;
    border: none;
    padding: 10px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    
  }

  .search-icon i {
    color: white;
    font-size: 18px;
  }
  
  .search-icon :hover{
    color:#555;
  }

  .latest-entries {
    padding: 10px;
    background-color: #FFFFFF;
    border-radius: 8px;
    margin-top: 20px;
  }

  .latest-entries h2 {
    font-size: 1.5rem;
    color: #003366;
    margin-bottom: 10px;
  }

  
  
  
  /* .card:hover {
    transform: scale(1.05);
  } */

  
  .breadcrumbs-box {
    padding: 15px;
    background-color: #fff; /* White background for contrast */
    border: 1px solid #f0eef2; /* Light border matching navbar background */
    /* Rounded corners */
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* Subtle shadow for depth */
     /* Spacing below the breadcrumbs box */
    z-index:1000;
    position:sticky;
  
  }

  /* Breadcrumb container styles */
  .breadcrumbs {
    font-size: 16px;
    display: flex;
    align-items: center;
  }

  /* Breadcrumb links */
  .breadcrumb {
    display: inline-block;
    margin-right: 10px;
    color: rgb(206 68 164);
    cursor: pointer;
    font-weight: bold;
    transition: color 0.2s ease;
  }

  .breadcrumb:hover {
    color: #f1c40f; /* Change color on hover */
    text-decoration: underline; /* Optional: underline on hover */
  }

  .breadcrumb:active {
    color: #8e44ad; /* Active state */
  }

  /* Breadcrumb separator */
  .breadcrumb-separator {
    margin-right: 10px;
    color: #333;
  }

  /* Active breadcrumb style */
  .breadcrumb.active {
    color: purple; /* Highlight active breadcrumb */
  }

  /* Optional: Adding a background color on hover */
  .breadcrumb:hover {
    background-color: #f0f0f0;
    border-radius: 4px;
    padding: 2px 5px;
  }
  /* .navbar {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: flex-start;
    gap: 15px;
    background-color: white;
    padding-top:10px;
    padding-left:5px;
    padding-bottom: 10px;
    position: sticky;
    top:100px;
  } */

  .category-card {
    background: #fff;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.6);
    
    width: 200px;
    height: 50px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    cursor: pointer;
    position: relative;
    transition: transform 0.2s, box-shadow 0.2s;
    font-weight: bold;
  }

  .category-card:hover {
    transform: translateY(-5px); /* Lift effect */
    background-color: rgb(230, 228, 228);; /* Change text color to golden-yellow */
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15); /* Add soft shadow */
}


  .category-card i {
    font-size: 20px;
    background: linear-gradient(125deg, rgb(85, 26, 139), rgb(206, 68, 164));
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
    margin-bottom: 10px;
  }
  
/* 
.category-card:hover i {
  color: rgb(230, 228, 228);
} */

  .category-card div {
    font-size: 16px;
    font-weight: bold;
    color: #333;
  }

  /* Dropdown container */
  .dropdown {
  display: none;
  position: absolute;
  top: 50px;
  left: 0; /* Align dropdown to the left edge of the page */
  min-width:300px; /* Occupy the entire width of the navbar */
  background-color: white; /* White background for dropdown */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5); /* Subtle shadow for depth */
  border-radius: 0; /* Remove border-radius for alignment */
  z-index: 1001;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.3s, visibility 0.3s;
  overflow-y: auto; /* Enable vertical scrolling if needed */
  overflow-x: auto; /* Disable horizontal scrolling */
  max-height: 400px; /* Limit the dropdown height */
}

.dropdown::-webkit-scrollbar {
  width: 6px; /* Set scrollbar width */
}

.dropdown::-webkit-scrollbar-thumb {
  background-color: #bcbcba; /* Set the color of the scrollbar thumb */
  border-radius: 3px; /* Rounded corners for the scrollbar thumb */
}

.dropdown::-webkit-scrollbar-thumb:hover {
  background-color: #555; /* Darker color when hovered */
}

.dropdown::-webkit-scrollbar-track {
  background: #f1f1f1; /* Light track color */
  border-radius: 3px; /* Rounded corners for the track */
}

.dropdown::-webkit-scrollbar-track:hover {
  background: #e1e1e1; /* Darker track color when hovered */
}

.dropdown::-webkit-scrollbar-corner {
  background-color: transparent; /* Remove the corner square */
}

.category-card:hover .dropdown {
  display: block;
  opacity: 1;
  visibility: visible;
}

/* Subcategory item */
.subcategory-item {
  padding: 12px 20px;
  text-align: left;
  color: #333; /* Maintain text color */
  cursor: pointer;
  font-size: 14px;
  font-weight: 500;
  border-bottom: 1px solid #f0eef2; /* Separator between items */
  transition: background-color 0.2s, padding-left 0.2s;
}
  /* Subcategory hover effect */
  

  

  .subcategory-item:last-child {
  border-bottom: none;
}

/* Subcategory hover effect */
.subcategory-item:hover {
  background-color:rgb(230, 228, 228);; /* Light hover background */ /* Change text color on hover */
  padding-left: 25px; /* Indentation effect */
}

/* Optional: Highlight the entire dropdown on hover */


/* Sticky navbar positioning */
.navbar {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
  gap: 15px;
  background-color: white;
  padding: 10px 5px;
  position: sticky;
  top: 85px;
  z-index:1000;
}
.overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5); /* Semi-transparent black background */
    z-index: 5; /* Ensure overlay is above all other content */
  }
  

/* .card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
} */


.card-image {
  width: 100%;
  height: 180px;
  object-fit: cover;
}

.card-title {
  font-size: 1.2rem;
  font-weight: bold;
  margin:10px;
  color: #333;
  text-decoration: none;
}

.card-description {
  margin-top:3px;
  margin-bottom:3px;
  margin-left:10px;
  color: #666;
  font-size: 0.9rem;
}

.card-footer {
  margin-top: auto;
  display: flex;
  justify-content: space-between;
  padding: 10px;
  border-top: 1px solid #eee;
  font-size: 0.85rem;
  color: #555;
}

.footer-item {
  display: flex;
  align-items: center;
  gap: 8px;
}

.icon-location::before {
   /* Replace with a location icon from your library if desired */
  font-size: 1rem;
}

.icon-date::before {
  /* Replace with a calendar icon from your library if desired */
  font-size: 1rem;
}


.scroll-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  overflow-x: hidden;
}

.card-container {
  display: flex;
  overflow-x: auto;
  gap: 1rem;
  padding: 1rem;
  scroll-behavior: smooth;
 
  /* Hide scrollbar in a clean way */
 /* For Firefox */
}

.card-container::-webkit-scrollbar {
  width: 3px; /* Set scrollbar width */
}

.card-container::-webkit-scrollbar-thumb {
  background-color:white; /* Set the color of the scrollbar thumb */
  border-radius: 3px; /* Rounded corners for the scrollbar thumb */
}

.card-container::-webkit-scrollbar-thumb:hover {
  background-color: white; /* Darker color when hovered */
}

.card-container::-webkit-scrollbar-track {
  background:white; /* Light track color */
  border-radius: 3px; /* Rounded corners for the track */
}

.card-container::-webkit-scrollbar-track:hover {
  background-color:white; /* Darker track color when hovered */
}

.card-container::-webkit-scrollbar-corner {
  background-color: transparent; /* Remove the corner square */
}
.card-container::-webkit-scrollbar-button {
  display: none; /* Hide the arrows */
}


.card {
  min-width: 300px; /* Ensure cards have a consistent width */
  background: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  padding: 1rem;
  position: relative;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;

}

.card-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 1rem;
}

.like-button, .delete-button {
  color:#781097;
  border: none;
  padding: 0.5rem;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
  background: none;
  font-size: 20px;
}

.like-button:hover, .delete-button:hover {
  color:#560d6c;
  transform: scale(1.05);
}
.dislike-button:hover{
color:#bcbcba;
transform: scale(1.05);
}





/* Styling for liked state */


</style>
