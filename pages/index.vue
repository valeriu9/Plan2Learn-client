<template>
  <div>
    <PopupTemplate ref="popupOpen" :currentResource="currentResource" @bookingObject="createBooking"/>
    <div class="container">
    <h1>Book a resource</h1>
    <table id="resources">
      <tbody>
      <tr>
        <th>Id</th>
        <th>Name</th>
        <th></th>
      </tr>
      <tr v-for="resource in resourceList" :key="resource.id">
        <td>{{resource.id}}</td>
        <td>{{resource.name}}</td>
        <td><button @click="openModal(resource)">book</button></td>
      </tr>
      </tbody>
    </table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
   components: {
    PopupTemplate: () => import('@/components/BookingModal')
  },
  mounted(){
    this.fetchData()
  },
  data(){
    return {resourceList: [], currentResource: {}}
  },
  methods:{
   async fetchData(){
      try{
        const res = await this.$axios.get(`https://localhost:7184/get-resources`)
        this.resourceList = res.data;
      }
      catch(e){
        console.log(e.message);
      }
    },
   async createBooking(booking){
     try{
       booking.resourceId = this.currentResource.id;
        const res = await this.$axios.post(`https://localhost:7184/create-booking`, {dateFrom: booking.dateFrom, dateTo: booking.dateTo, resourceId: booking.resourceId, bookedQuantity: booking.bookedQuantity });
        if(res.data.statusType === "error"){
          window.alert(res.data.message);
        }
        if(res.data.statusType === "success"){
          window.alert(res.data.message)
          this.closeModal();
          this.resetData();
        }
      }
      catch(e){
        console.log(e.message);
      }
    },
    resetData(){
      this.currentResource = {};
    },
    openModal (currentResourceClicked) {
      this.$refs.popupOpen.open();
      this.currentResource = currentResourceClicked;
    },
    closeModal () {
      this.$refs.popupOpen.close();
    },
  }
}
</script>

<style scoped>
#resources {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 1000px;
  display: flex;
  justify-content: center;
}

#resources td, #resources th {
  border: 1px solid #ddd;
  width: 240px;
  padding: 12px;
}

#resources tr:nth-child(even){background-color: #f2f2f2;}

#resources tr:hover {background-color: #ddd;}

#resources th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #04AA6D;
  color: white;
}
.container{
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
</style>
