<template>
    <div>
      <!-- Header Navbar -->
      <header>
        <!-- Logo -->
        <div class="logo">
          <img src="../assets/image/logo.png" alt="Logo">
        </div>
        
        <Button @click="close"  icon="pi pi-times" class="p-button-danger" />
      </header>
    </div>
  </template>
  
  <script>
  import Button from 'primevue/button';
  export default {
    components:{
      Button
    },
    methods:
    {
      close()
      {
        window.close();
      }
    }
  }
  </script>
  
  <style >
   footer,header
  {
    background-color: #333 ;
  }
  /* Add your styles for the header, logo, and navigation links here */
  header {
    height:75px;
    
    color: white;
    padding: 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .logo img {
    height: 50px; /* Adjust the height as needed */
  }
  
  nav ul {
    list-style-type: none;
    display: flex;
    gap: 20px;
  }
  
  nav a {
    text-decoration: none;
    color: white;
    font-weight: bold;
  }
 
  </style>
  