
<template>
  <div id="app">
    <HeaderComponent />
    <main>
      <TreeTable class="custom-tree-table" />
    </main>
    <FooterComponent />
    <Toast position="bottom-right" />
  </div>
</template>

<script>
import TreeTable from './components/TableComponent.vue';
import HeaderComponent from './components/HeaderComponent.vue';
import FooterComponent from './components/FooterComponent.vue'
import tree from './components/TreeTable1'
import Toast from 'primevue/toast';

export default {
  components: {
    Toast,
    TreeTable,
    HeaderComponent,
    FooterComponent,
    tree
  }
};
</script>

<style scoped>
#app {
  display: flex;
  flex-direction: column;
  max-height: 100vh; 
}

main {
  flex: 1 0 auto; 
  padding-top: 10px; 
  text-align: center;
  color: #2c3e50;
  min-height: 74vh;

}

.custom-tree-table {
  width: 100%; 
  margin: 0 auto; 
}
</style>
