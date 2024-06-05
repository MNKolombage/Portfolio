<template>
  <div>
    <h1>{{ name }}</h1>
    <div v-if="pendingProjects">Loading</div>
    <div v-if="errorProjects">Error</div>
    <ul>
      <div v-for="project in projects" :key="project.name">
        <h2>{{ project.name }}</h2>
        <p>{{ project.description }}</p>
      </div>
    </ul>
  </div>
  <div v-if="pendingBlogs">Loading</div>
  <div v-if="errorBlogs">Error</div>

  <ul>
    <div v-for="blog in blogs" :key="blog.name">
      <h2>{{ blog.name }}</h2>
      <p>{{ blog.description }}</p>
    </div>
  </ul>

  <div>
    <h1>Create a new project</h1>
    <form @submit.prevent="createProject">
      <div>
        <label for="name">Project Name:</label>
        <input type="text" v-model="newProject.name" id="name" required />
      </div>
      <div>
        <label for="description">Project Description:</label>
        <textarea v-model="newProject.description" id="description" required></textarea>
      </div>
      <button type="submit">Create Project</button>
    </form>
  </div>
</template>

<script setup>
const name = "Maleesha Nuwanthi";
const {
  data: projects,
  pendingProjects,
  errorProjects,
} = useFetch("http://localhost:5000/projects");

const {
  data: blogs,
  pendingBlogs,
  errorBlogs,
} = useFetch("http://localhost:5000/blogs");

const newProject = ref({
  name: '',
  description: '',
});

const createProject = async () => {
  console.log(newProject.value);

  try {
    const response = await fetch('http://localhost:5000/projects', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newProject.value),
    });

    if(!response.ok) {
      throw new Error('Failed to create project');
    }

    const result = await response.json();
    projects.value.push(result);  //Add the new project to the result (No need to refresh)

    //Clear the form
    newProject.value.name = '';
    newProject.value.description = '';
  } catch (error) {
    console.error('Error:', error);
  }
};
</script>

<style>
</style>
