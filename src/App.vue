<template>
  <div class="relative isolate overflow-hidden bg-gray-900">
    <div class="px-6 py-24 sm:px-6 sm:py-32 lg:px-8">
      <div class="mx-auto max-w-2xl text-center">
        <h2 class="text-3xl font-bold tracking-tight text-white sm:text-4xl">☕ Cafeteria</h2>
        <p class="mx-auto mt-2 max-w-xl text-md leading-8 text-gray-300 text-lg">What would you like today?</p>
        <div class="mt-6 mx-32">

          <ul role="list" class="mt-3 mb-6 grid grid-cols-1 gap-5 sm:grid-cols-2 sm:gap-6 lg:grid-cols-2">
            <li class="col-span-1 flex rounded-md shadow-sm">
              <div class="flex w-16 flex-shrink-0 items-center justify-center bg-green-700 rounded-l-md text-sm font-medium text-white">
                <svg class="h-9" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6">
                  <path strokeLinecap="round" strokeLinejoin="round" d="M12 6v12m-3-2.818l.879.659c1.171.879 3.07.879 4.242 0 1.172-.879 1.172-2.303 0-3.182C13.536 12.219 12.768 12 12 12c-.725 0-1.45-.22-2.003-.659-1.106-.879-1.106-2.303 0-3.182s2.9-.879 4.006 0l.415.33M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              </div>
              <div class="flex flex-1 items-center justify-between truncate rounded-r-md border-b border-r border-t border-green-700 bg-green-900">
                <div class="flex-1 truncate px-4 py-2 text-sm">
                  <p class="font-bold text-white">Meal earnings</p>
                  <p class="text-gray-100 font-semibold">{{ earningsMealSum.toFixed(2) }}</p>
                </div>
              </div>
            </li>
            <li class="col-span-1 flex rounded-md shadow-sm">
              <div class="flex w-16 flex-shrink-0 items-center justify-center bg-green-700 rounded-l-md text-sm font-medium text-white">
                <svg class="h-9" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6">
                  <path strokeLinecap="round" strokeLinejoin="round" d="M12 6v12m-3-2.818l.879.659c1.171.879 3.07.879 4.242 0 1.172-.879 1.172-2.303 0-3.182C13.536 12.219 12.768 12 12 12c-.725 0-1.45-.22-2.003-.659-1.106-.879-1.106-2.303 0-3.182s2.9-.879 4.006 0l.415.33M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              </div>
              <div class="flex flex-1 items-center justify-between truncate rounded-r-md border-b border-r border-t border-green-700 bg-green-900">
                <div class="flex-1 truncate px-4 py-2 text-sm">
                  <p class="font-bold text-white">Total earnings</p>
                  <p class="text-gray-100 font-semibold">{{ earningsTotalSum.toFixed(2) }}</p>
                </div>
              </div>
            </li>
          </ul>

          <button @click="askBusiness" class="px-3 py-1 text-gray-900 bg-white rounded-md hover:bg-gray-100 focus:outline-none focus:ring-2">
              How is business?
          </button>
            
          <span class="isolate inline-flex rounded-md shadow-sm mt-6">
            <button @click="selectMeal('Dagens')" type="button" class="relative inline-flex items-center rounded-l-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 ring-1 ring-inset ring-gray-300 hover:bg-gray-100 focus:z-10">Dagens ({{ getItemServingsLeft('Dagens') }} left)</button>
            <button @click="selectMeal('Vegetar')" type="button" class="relative -ml-px inline-flex items-center bg-white px-3 py-2 text-sm font-semibold text-gray-900 ring-1 ring-inset ring-gray-300 hover:bg-gray-100 focus:z-10">Vegetar ({{ getItemServingsLeft('Vegetar') }} left)</button>
            <button @click="selectMeal('Halal')" type="button" class="relative -ml-px inline-flex items-center rounded-r-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 ring-1 ring-inset ring-gray-300 hover:bg-gray-100 focus:z-10">Halal ({{ getItemServingsLeft('Halal') }} left)</button>
          </span>

          <p class="text-white mt-5" id="response-paragraph" v-html="response"></p>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
body {
  background-color: #111827;
}
</style>


<script>
export default {
  data() {
    return {
      items: [['Dagens', 53.90, 15], ['Vegetar', 42.50, 2], ['Halal', 59.90, 5]],
      earningsMealSum: 0,
      earningsTotalSum: 0,
      response: '',
      shouldResetEarnings: false
    };
  },
  methods: {
    removeMealServing(mealName, numServings) {
      for (let meals of this.items) {
        if (meals[0] === mealName) {
          meals[2] -= numServings;
          break;
        }
      }
    },
    sellMeal(mealName, numServings) {
      for (let meals of this.items) {
        if (meals[0] === mealName) {
          for (let x = 0; x < numServings; x++) {
            this.earningsMealSum += meals[1];
            this.earningsTotalSum += meals[1];
          }
          if (this.earningsMealSum >= 200) {
            this.resetEarnings(); // Reset earnings immediately
            setTimeout(() => {
              this.displayResponse("Cash registry emptied. Thieves be warned!<br>"); // Display the message after a slight delay
            }, 10);
          }
          break;
        }
      }
    },
    noServingsLeft(mealName) {
      for (let meals of this.items) {
        if (meals[0] === mealName) {
          return meals[2] === 0;
        }
      }
      return false;
    },
    emptyCashRegistry() {
      this.earningsMealSum = 0;
      this.displayResponse("Cash registry emptied. Thieves be warned!<br>");
    },
    cashier(mealName, numServings) {
      if (!this.noServingsLeft(mealName)) {
        this.sellMeal(mealName, numServings);
        this.removeMealServing(mealName, numServings);
        this.displayResponse(`${mealName}, here you go.<br>`);
      } else {
        this.displayResponse(`Sorry, no more left of ${mealName} today.<br>`);
      }
    },
    displayResponse(response) {
      this.response = response; // Update the response data property
    },
    getItemServingsLeft(mealName) {
      for (let meals of this.items) {
        if (meals[0] === mealName) {
          return meals[2];
        }
      }
      return 0;
    },
    resetEarnings() {
      this.earningsMealSum = 0;
    },
    selectMeal(mealName) {
      if (this.items.some(nestedMealList => nestedMealList[0] === mealName)) {
        this.cashier(mealName, 1);
      } else {
        this.displayResponse(`${mealName} is not on the menu!<br>`);
      }
    },
    askBusiness() {
      if (this.earningsTotalSum === 0) {
        this.displayResponse("Not good so far. No one seems to be hungry today!<br>");
      } else if (this.earningsTotalSum < 499) {
        this.displayResponse("Alright. Could have been better<br>");
      } else if (this.earningsTotalSum > 500) {
        this.displayResponse("Excellent! Lots of hungry students around today.<br>");
      }
    }
  }
};
</script>
