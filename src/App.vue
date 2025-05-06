<template>
  <div class="app">
    <!-- Header -->
    <div class="header">
      <div class="search-bar-container">
        <input
          v-model="search"
          type="text"
          placeholder="Search quotes..."
          class="search-input"
        />
        <img src="./assets/search-glass.png" alt="glass" class="search-glass" />
      </div>
      <img src="./assets/3line-bar.png" alt="sidebar" @click="toggleSidebar" class="sidebar" />
    </div>

    <!-- Quote List -->
    <div v-for="quote in filteredQuotes" :key="quote.id" class="quote-item">
      <button @click="openModal(quote)" class="quote-button">
        {{ quote.name }}
      </button>
    </div>

    <!-- Quote Modal -->
    <transition name="slide-fade">
      <div v-if="showModal" class="modal-overlay">
        <div class="modal-header">
          <button @click="closeModal" class="back-button">
            <img src="./assets/left-arrow.png" alt="" style="max-height:30px;">
          </button>
          <p class="quote-title">{{ selectedQuote.name }}</p>
        </div>

        <div class="modal">
          <ul v-if="Array.isArray(selectedQuote.meanings)" class="quote-meaning">
            <li v-for="(item, index) in selectedQuote.meanings" :key="index">{{ item }}</li>
          </ul>
          <p v-else class="quote-meaning">{{ selectedQuote.meanings }}</p>
        </div>
      </div>
    </transition>

    <!-- Sidebar Modal -->
    <transition name="sidebar-slide">
      <div v-if="showSidebar" class="sidebar-overlay">
        <div class="sidebar-modal">
          <div class="sidebar-content">
            <button @click="toggleSidebar" class="close-sidebar">
              <img src="./assets/left-arrow.png" alt="" style="max-height:30px;">
            </button>
            <div class="sidebar-btn">
              <button @click="goHome">Home</button>
              <button @click="toExit">Exit</button>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>


<script setup>
import { App } from '@capacitor/app'
import { ref, computed } from 'vue'

const quotes = ref([
  {
    id: 1,
    name: 'Love',
    meanings: [
      'Love should be embraced in the present, not postponed for the perfect time.',
      'Expressing love today matters more than planning for a future that may never come.',
      'Love thrives when it\'s lived boldly, without fear or hesitation.'
    ]
  },
  {
    id: 2,
    name: 'Family',
    meanings: [
      'Life is short—make every moment with family meaningful before it’s too late.',
      'Death reminds us to forgive, love deeper, and appreciate the presence of family.',
      'Even in mortality, family bonds offer something eternal—memories and legacies.'
    ]
  },
  {
    id: 4,
    name: 'Friends',
    meanings: [
      'True friendship helps you accept life’s ups and downs with peace.',
      'The friends you meet—even in hardships—are part of your destined path.',
      'Embrace every friend as part of your story, even if their time in it is brief.'
    ]
  },
  {
    id: 5,
    name: 'Birthday',
    meanings: [
      'Each birthday is a reminder to accept your life journey, with all its twists.',
      'Celebrate growing older by embracing the path that shaped who you are.',
      'Rather than fear aging, welcome it as part of the life you’re meant to live.'
    ]
  },
  {
    id: 6,
    name: 'Broken',
    meanings: [
      'Being broken is not a flaw—it’s a chapter in the story you\'re meant to tell.',
      'Accepting your brokenness can lead to unexpected healing and growth.',
      'The scars you carry are reminders that you’ve survived your fate.'
    ]
  },
  {
    id: 7,
    name: 'Motivational',
    meanings: [
      'True motivation comes from owning your journey—even its failures.',
      'Rather than resist challenges, use them as fuel for your purpose.',
      'Motivation grows when you stop fighting fate and start working with it.'
    ]
  },
  {
    id: 8,
    name: 'Sad',
    meanings: [
      'Sadness is part of life’s balance; loving your fate means accepting sorrow too.',
      'Your sadness has shaped your strength—don’t reject that part of you.',
      'Loving your fate includes being kind to yourself in your lowest moments.'
    ]
  },
  {
    id: 9,
    name: 'Happines',
    meanings: [
      'Real happiness is found when you stop wishing for another life and embrace your own.',
      'Loving your fate frees you from comparison and lets you find joy where you are.',
      'The happiest people don’t have perfect lives—they’ve accepted their imperfect ones.'
    ]
  },
  {
    id: 10,
    name: 'Success',
    meanings: [
      'Success is not just reaching goals, but appreciating the journey you took.',
      'Your version of success is valid—don’t let others define it for you.',
      'Even failures that led to success were part of your necessary fate.'
    ]
  },
  {
  id: 11,
  name: 'Courage',
  meanings: [
    'Courage isn’t the absence of fear—it’s acting despite it.',
    'Bravery is born when you choose growth over comfort.',
    'Even small acts of courage shape your destiny in big ways.'
  ]
},
{
  id: 12,
  name: 'Forgiveness',
  meanings: [
    'Forgiving others frees you more than it frees them.',
    'Letting go doesn’t erase the past—it releases its power over you.',
    'True strength is shown when you choose peace over resentment.'
  ]
},
{
  id: 13,
  name: 'Hope',
  meanings: [
    'Hope is the light you carry through the darkest tunnels.',
    'Even the smallest hope can grow into something life-changing.',
    'Believing in a better tomorrow gives meaning to today.'
  ]
},
{
  id: 14,
  name: 'Healing',
  meanings: [
    'Healing takes time—allow yourself to move at your own pace.',
    'Some wounds don’t disappear, but they do stop hurting.',
    'Your scars are not shameful—they are symbols of survival.'
  ]
},
{
  id: 15,
  name: 'Teammates',
  meanings: [
    'Great teammates lift each other up, not compete to stand alone.',
    'Teamwork isn’t about being the best—it’s about bringing out the best in others.',
    'Trust is the foundation that turns individuals into a real team.'
  ]
},
{
  id: 16,
  name: 'Patience',
  meanings: [
    'Great things take time—so does growth.',
    'Patience allows progress to unfold naturally.',
    'Waiting with purpose is not weakness—it’s wisdom.'
  ]
},
{
  id: 17,
  name: 'Growth',
  meanings: [
    'Growth begins where comfort ends.',
    'You can’t evolve if you’re always avoiding change.',
    'Real growth is messy, personal, and necessary.'
  ]
},
{
  id: 18,
  name: 'Gratitude',
  meanings: [
    'Gratitude turns what you have into enough.',
    'A thankful heart invites joy.',
    'Every moment you appreciate becomes more meaningful.'
  ]
},
{
  id: 19,
  name: 'Self-Worth',
  meanings: [
    'You are enough—even when the world says otherwise.',
    'Don’t measure your value by external approval.',
    'Self-worth is knowing you deserve peace, not proving it.'
  ]
},
{
  id: 20,
  name: 'Change',
  meanings: [
    'Change is uncomfortable, but necessary for transformation.',
    'Life improves when you stop resisting change.',
    'Every ending creates space for a new beginning.'
  ]
},
{
  id: 21,
  name: 'Perseverance',
  meanings: [
    'Keep going—even when progress feels invisible.',
    'Success often shows up after the last time you almost gave up.',
    'Endurance is a quiet kind of strength.'
  ]
},
{
  id: 22,
  name: 'Kindness',
  meanings: [
    'Kindness leaves echoes longer than anger ever could.',
    'Being kind is never wasted—even if unnoticed.',
    'A gentle word can change someone’s whole day.'
  ]
}

])

const search = ref('')
const showModal = ref(false)
const showSidebar = ref(false)
const selectedQuote = ref({})

const filteredQuotes = computed(() =>
  quotes.value.filter(q =>
    q.name.toLowerCase().includes(search.value.toLowerCase())
  )
)

const openModal = (quote) => {
  selectedQuote.value = quote
  showModal.value = true
}

const closeModal = () => {
  showModal.value = false
}

const toggleSidebar = () => {
  showSidebar.value = !showSidebar.value
}
const goHome = () => {
  showModal.value = false
  showSidebar.value = false
}
const toExit = () => {
  App.exitApp();
}
</script>

<style>
body {
  margin: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: pink;
}

.app {
  height: calc(100vh - 40px);
  width: calc(100vw - 40px);
  padding: 90px 20px 20px 20px;
}
.quote-meaning li {
  margin-bottom: 12px;
  font-size: 18px;
  color: #444;
  list-style-type: disc;
  padding-left: 20px;
}

.header {
  position: fixed;  
  top: 0;
  left: 0;
  width: calc(100% - 40px);
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  background-color: pink;
  z-index: 998;  
  padding: 20px; 
}

.search-bar-container {
  display: flex;
  background-color: rgb(255, 194, 252);
  padding: 8px;
  border-radius: 1rem;
}

.search-input {
  outline: none;
  font-size: 16px;
  border: none;
  background-color: transparent;
}

.search-glass {
  max-width: 25px;
}

.sidebar {
  max-width: 30px;
  cursor: pointer;
}

.quote-item {
  display: flex;
  flex-direction: column;
}

.quote-button {
  background:rgb(255, 222, 227);
  border: none;
  color: black;
  font-size: 18px;
  cursor: pointer;
  text-align: center;
  font-family: 'Courier New', Courier, monospace;
  font-weight: bold;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 1rem;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: pink;
  display: flex;
  flex-direction: column;
  justify-content: center;
  z-index: 999;
}
.modal-header{
  position: fixed;
  z-index: 999;
  top:0;
  left:0;
  display: flex;
  justify-content: center;
}
.modal {
  background-color: #fff;
  padding: 20px;
  margin: 20px;
  border-radius: 8px;
  max-width: 500px;
  width: calc(100% - 80px);
  position: relative;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

.slide-fade-enter-active,
.slide-fade-leave-active {
  transition: all 0.6s ease;
}

.slide-fade-enter-from {
  transform: translateX(100%);
  opacity: 0;
}

.slide-fade-enter-to {
  transform: translateX(0);
  opacity: 1;
}

.slide-fade-leave-from {
  transform: translateX(0);
  opacity: 1;
}

.slide-fade-leave-to {
  transform: translateX(100%);
  opacity: 0;
}

.back-button {
  background: none;
  border: none;
  font-size: 18px;
  cursor: pointer;
}


.quote-title {
  position: relative;
  top: -3px;
  font-size: 24px;
  font-weight: bold;
}

.quote-meaning {
  margin-top: 12px;
  font-size: 18px;
  color: #444;
}

.sidebar-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: transparent;
  z-index: 998;
  display: flex;
  justify-content: flex-end;
}

.sidebar-modal {
  position: relative;
  top: 70px;
  right: 0;
  width: 80%;
  height: calc(100% - 70px);
  background-color:rgb(225, 224, 224);
  box-shadow: -4px 0 12px rgba(0, 0, 0, 0.3);
  z-index: 999;
  padding: 20px;
  box-sizing: border-box;
}

.sidebar-slide-enter-active,
.sidebar-slide-leave-active {
  transition: transform 0.4s ease;
}

.sidebar-slide-enter-from,
.sidebar-slide-leave-to {
  transform: translateX(100%);
}

.sidebar-slide-enter-to,
.sidebar-slide-leave-from {
  transform: translateX(0%);
}

.sidebar-content {
  height: 100%;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.sidebar-btn{
  flex: 1; 
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.sidebar-btn button {
  background: pink;
  border: none;
  color: black;
  font-size: 18px;
  cursor: pointer;
  text-align: center;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 1rem;
}
.close-sidebar {
  align-self: flex-start;
  background: none;
  cursor: pointer;
  border:none;
}
</style>
