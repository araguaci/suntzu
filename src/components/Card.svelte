<script lang="ts">
  interface QuoteType { 
    suntzuquote: string;
  }

  let idIndex = -1;
  let isLoading = true;
  let isButtonDisabled = true;
  let currentDate = new Date();
  let promise = fetchQuote();

  $: {
    const newDate = new Date();
    newDate.setTime(currentDate.getTime() + 24 * 60 * 60 * 1000);
    isButtonDisabled = newDate > new Date();
  }

  async function fetchQuote() {
    isLoading = true;
    currentDate.setHours(currentDate.getHours() - 1);
    const response = await fetch("https://api.zerosheets.com/v1/rsx", {
        method: "GET",
        headers: {
            Authorization: "Bearer TxIVjmoMot6vMCGzSodWh9UxzuzBFtpv"
        }
    });
    try {
      const data = await response.json(); 
      idIndex = parseInt(Math.random() * (372));       
      const suntzuquote = data[idIndex].suntzuquote;
      console.log(suntzuquote);
      return suntzuquote;
    } catch (error) {
      isLoading = false;
      throw new Error("Failed fetch quote!");
    }  
 
  }

  function content(divSelector, value) {
    console.log(value);
    document.querySelector(divSelector).innerHTML = "'" + value + "'";
  }

  function handleClick(next: boolean) {
    const newDate = new Date();

    if (next) newDate.setTime(currentDate.getTime() + 24 * 60 * 60 * 1000);
    else newDate.setTime(currentDate.getTime() - 24 * 60 * 60 * 1000);
    currentDate = newDate;
    promise = fetchQuote(); 
  }

</script>

<main class="card"> 
  {#await promise}
	<h2>Awaiting...</h2>
  {:then suntzuquote}
  <h1>{suntzuquote}</h1>
  <h2>~~Sun Tzu</h2>
  {:catch error}
    <p style="color: red">{error.message}</p>
  {/await}
</main>

<style lang="css">
  /* main */
  main {
    @apply flex flex-col items-center justify-center relative;
  }

  /* arrow */
  .left {
    @apply left-0;
  }

  .right {
    @apply right-0;
  }

  button {
    @apply sm:opacity-0 duration-300 text-3xl w-fit p-2 absolute top-1/2 -translate-y-1/2 text-white bg-black/[0.5];
  }

  button:disabled {
    @apply cursor-not-allowed;
  }

  section[aria-label="image"]:hover button {
    @apply sm:opacity-100;
  }

  /* error */
  main > h1 {
    @apply text-3xl font-semibold text-white bg-black/[0.5] p-5 rounded-md;
  }
  main > h2 {
    @apply text-2xl font-semibold text-white p-5 rounded-md;
  }

  /* loading */
  .loading {
    translate: -50% -50%;
    @apply animate-custom-spin border-4 border-white w-20 h-20 absolute top-1/2 left-1/2;
  }

  .loading::before {
    content: "";
    @apply absolute animate-custom-stretch top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 border-4 border-white w-16 h-16;
  }

  /* image */
  section[aria-label="image"] {
    box-shadow: 0 0 25px #00bccaa9;
    @apply rounded-md relative overflow-y-hidden;
  }

  section[aria-label="image"] > img {
    @apply rounded-md max-h-[80vh] sm:max-h-[75vh] aspect-9/13 object-cover object-center;
  }

  /* text */
  section[aria-label="text"] {
    background-color: rgb(0, 0, 0, 0.5);
    @apply -translate-y-[100%];
    @apply flex flex-col gap-2 sm:gap-5 absolute w-full p-4 sm:p-5 text-white rounded-b-md;
    @apply rounded-md object-cover object-center;
  }

  section[aria-label="text"] > div:first-of-type {
    @apply text-3xl font-bold flex flex-col gap-1 text-white;
  }

  section[aria-label="text"] > div:first-of-type::after {
    content: "";
    @apply h-1 w-32 origin-left duration-300 delay-200 bg-green-600;
  }

  section[aria-label="text"] > div:last-of-type {
    @apply flex flex-col sm:gap-1;
  }
</style>
