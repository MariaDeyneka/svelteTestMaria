<script>
  import "./styles/app.css";
  import { writable } from "svelte/store";
  import { mockedData } from "./mock/data";
  const people = writable(mockedData);
  let result;
  let isFocused = false;
  const history =
    localStorage.getItem("history") !== null
      ? JSON.parse(localStorage.getItem("history"))
      : [];
  const some = writable(history);
  const getHistory = () => {
    let existingItem = localStorage.getItem("history");
    return JSON.parse(existingItem);
  };
  const inputHistory = () => {
    const value = document.getElementById("person-search").value;
    history.push(value);
    localStorage.setItem("history", JSON.stringify(history));
    window.location.reload();
  };

  const onFocus = () => (isFocused = true);
  const onBlur = () => (isFocused = false);
  const checkIfThree = () => {
    const word = document.getElementById("person-search");
    result = word?.value?.length >= 2;
  };

  let opened = false;
  const onOpen = (some) => {
	  console.log(some === history[history.length-1]);
	  if (some === history[history.length-1]){
		lastPersonData()
		return  opened = true;
	  }
	  opened = false;
	  
  };
  const onClose = () => (opened = false);
  let userData = {};
  const lastPersonData = () => {
	console.log(history[history.length-1]?.split(' ')[2]);
	  mockedData.forEach(element => {
		
			if(element.id === history[history.length-1]?.split(' ')[2]) {
				console.log(history[history.length-1]?.split(' ')[2]);
				return userData = element
			}
		});
  }
  console.log(lastPersonData());
</script>

<svelte:window on:keydown={checkIfThree} />
<div class="flex flex-row justify-between items-center px-9">
  <div class="space-y-1">
    <span class="burger" />
    <span class="burger" />
    <span class="burger" />
  </div>
  <!-- svelte-ignore a11y-img-redundant-alt -->
  <img src="images/main-icon.png" alt="header-image" />
  <form class="flex items-center w-2/4">
    <label for="voice-search" class="sr-only">Search</label>
    <div class="relative w-full">
      <input
        type="search"
        id="person-search"
        class="border border-gray-300 text-gray-900 text-sm rounded-md focus:ring-gray-500 focus:border-gray-500 block w-full pl-5 p-2.5"
        placeholder="Search"
        required
        autocomplete="off"
        list="mocked-data"
        on:focus={onFocus}
        on:blur={onBlur}
      />

      {#if result}
        <datalist id="mocked-data">
          {#each $people as person}
            <option
              >{person.first_name} {person.last_name} {person.id} {person.relationship}</option
            >
          {/each}
        </datalist>
      {/if}
    </div>
  </form>
  <button
    class="border rounded-md bg-gray-300 text-white w-40 h-11 block"
    id="create"
    on:click={inputHistory}>Search</button
  >
</div>
<div
  class={isFocused && !result
    ? "history_search pt-4 pl-4 pr-4 absolute shadow-xl h-auto flex flex-row"
    : "hidden"}
>
  {#if some}
    <div class="flex flex-col w-3/5">
      {#each [...$some].reverse() as history}
        <p>{history?.split(" ")[3]}</p>

        <div class="flex flex-row justify-between items-center pb-4">
          <p class="flex flex-row justify-between items-center">{history}</p>

          {#if history?.split(" ")[0] === $some[$some.length - 2]?.split(" ")[0] || history?.split(" ")[0] === $some[$some.length - 1]?.split(" ")[0]}
            <button
              class="border rounded-md w-20 border-lime-300 bg-lime-300 text-lime-800"
              >new</button
            >
          {/if}
          <button
            on:focus={opened}
            on:mouseover={() => {
              onOpen(history);
            }}
            on:mouseleave={() => {
              onClose();
            }}>></button
          >
        </div>
      {/each}
    </div>
  {/if}
  {#if some}
    <div class={opened ? "w-2/5" : "hidden"}>
      <div class="flex flex-col">
        <div class="flex flex-row">
	      <!-- svelte-ignore a11y-missing-attribute -->
	      <!-- <img src={`images/profile_pics/small/${userData.avatar}`}> -->
          {userData.first_name}
          {userData.last_name}
          {userData.id}
        </div>
        <hr />
        <div class="">
          <ul>
            <li>Membership: {userData.membership}</li>
            <li>Phone: {userData.phone}</li>
            <li>Email: {userData.email_address}</li>
          </ul>
        </div>
    	<div class="flex flex-row justify-evenly w-full">
			<!-- svelte-ignore a11y-img-redundant-alt -->
			<button class="icons-button"><img src="static/chat.svg" alt="chat-icon" class="w-5" /></button>
			<!-- svelte-ignore a11y-img-redundant-alt -->
			<button class="icons-button"><img src="images/phone.svg" alt="phone-icon" class="w-5" /></button>
			<!-- svelte-ignore a11y-img-redundant-alt -->
			<button class="icons-button"><img src="images/location.svg" alt="location-icon" class="w-5" /></button>
     	 </div>
	  </div>
    </div>
  {/if}
</div>

<style lang="postcss" global>
  .burger {
    @apply block w-8 h-1 bg-gray-500 rounded-md;
  }
  .icons-button{
	  @apply border rounded-full bg-gray-300 w-8 h-8 flex items-center justify-center;
  }
  input[type="search"]::-webkit-search-cancel-button {
    -webkit-appearance: searchfield-cancel-button;
  }
  input::-webkit-calendar-picker-indicator {
    display: none !important;
  }
  .history_search {
    width: 48%;
    left: 33.5rem;
    top: 4.3rem;
  }
  .text {
    display: hidden;
  }
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
</style>
