<template>
  <div v-if="stories.length > 0">
    <h2 style="margin-left: 10px;">Latest Stories</h2>
    <ul>
      <li v-for="(story, index) in stories" :key="index">
        <a :href="story.link">{{ story.title }}</a>
      </li>
    </ul>
  </div>
  <div v-else>
    Loading...
  </div>
</template>

<script>
export default {
  data() {
    return {
      stories: []
    }
  },
  mounted() {
    const xhr = new XMLHttpRequest()
    xhr.open('GET', 'http://localhost:3001/getTimeStories', true)
    xhr.onreadystatechange = () => {
      if (xhr.readyState == 4) {
        if (xhr.status == 200) {
          const htmlContent = xhr.responseText
          const stories = extractStoriesFromHTML(htmlContent)
          this.stories = stories
        } else {
          console.error('Error fetching data')
        }
      }
    }
    xhr.send()
  }
}

function extractStoriesFromHTML(html) {
  const trimmedHtml = html.trim()
  if (trimmedHtml.length == 0) {
    return []
  }
  const startIndex = trimmedHtml.indexOf("[")
  const endIndex = trimmedHtml.lastIndexOf("]")
  if (startIndex == -1 || endIndex == -1 || endIndex <= startIndex) {
    return []
  }
  const jsonStr = trimmedHtml.substring(startIndex + 1, endIndex)
  const stories = []
  const storyStrs = jsonStr.split('},')
  for (let i = 0; i < storyStrs.length; i++) {
    const storyStr = storyStrs[i].trim()
    const titleMatch = storyStr.match(/"title":"(.*?)"/)
    const linkMatch = storyStr.match(/"link":"(.*?)"/)
    if (titleMatch && linkMatch) {
      const title = titleMatch[1]
      const link = linkMatch[1]
      stories.push({ title, link })
    }
  }
  return stories
}
</script>