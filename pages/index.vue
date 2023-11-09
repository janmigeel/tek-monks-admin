<template>
  <div>
    <button @click="getLatestStories">Get Latest Stories</button>
    <div v-for="(story, index) in stories" :key="index" style="padding: 10px;">
      <a :href="story.link" target="_blank">{{ story.title }}</a>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      stories: []
    }
  },
  methods: {
    async getLatestStories() {
      try {
        const response = await fetch('http://localhost:3001/getTimeStories')
        const data = await response.json()
        this.stories = data;
      } catch (error) {
        console.error('Error fetching latest stories:', error)
      }
    }
  }
}
</script>