<template>
  <nav>
    <ul>
      <li v-for="link in links" :key="link.id">
        <a @click="chg(link.path)">{{ link.label }}</a>

      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  data() {
    return {
      links: [
        { id: 1, label: 'Home', path: 'Home' },
        { id: 2, label: 'ChetApp', path: 'ChetApp' },
        // Add more links as needed
      ]
    };
  },
  methods:{
    chg(to){
      this.$emit('chg', to);
    }
  }
};
</script>

<style scoped>
nav {
  background-color: #333;
  padding: 10px;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
}
li {
  margin-right: 15px;
}

a {
  text-decoration: none;
  color: white;
  font-weight: bold;
}

a:hover {
  text-decoration: underline;
}
</style>
