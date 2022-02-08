<template>
  <div class="wrapper">
    <div class="normal-window" :class="showModal ? 'is-active' : ''" @click="overlayClick" />
    <div class="popup-window" :class="showModal ? 'is-active' : ''">
      <div class="content-wrapper">
        <p>Booking {{currentResource.name}}</p>
        <div class="input-row"><p>Date from:</p> <input v-model="dateFrom" type="datetime-local"></div>
        <div class="input-row"><p>Date to:</p> <input v-model="dateTo" type="datetime-local"></div>
        <div class="input-row"><p>Quantity:</p> <input v-model="quantity" type="number"></div>
        <button class="book-button" @click="createBooking()">Book</button>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  layout: 'default',
  props: {
    enableOverlayClick: {
      type: Boolean,
      default: true
    },
    currentResource:{
      type: Object,
      default: ()=>{}
    }
  },
  data () {
    return { showModal: false, dateFrom: new Date(), dateTo: new Date(), quantity: 0 };
  },
  beforeDestroy () {
    this.close();
  },
  methods: {
    open () {
      this.showModal = true;
      const x = window.scrollX;
      const y = window.scrollY;
      window.onscroll = function () {
        window.scrollTo(x, y);
      };
      this.$emit('opened');
    },
    close () {
      this.showModal = false;
      window.onscroll = function () {
      };
      this.$emit('closed');
    },
    overlayClick () {
      if (this.enableOverlayClick) {
        this.close();
      }
    },
    createBooking(){
      const bookingObj = {dateFrom : new Date(this.dateFrom).toISOString(), dateTo: new Date(this.dateTo).toISOString(), bookedQuantity: parseInt(this.quantity)};
      this.$emit("bookingObject", bookingObj);
    }
  }
};
</script>

<style scoped>
.normal-window {
  z-index: 100;
  position: fixed;
  background: rgba(0, 0, 0, 0.5);
  opacity: 0;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  transition: 0s;
  pointer-events: none;

}
.normal-window.is-active {
    opacity: 0.97;
    pointer-events: all;
    transition: 0.5s;
  }

.popup-window {
  z-index: 200;
  position: fixed;
  pointer-events: none;
  background-color: white;
  box-shadow: 0 10px 16px 0 rgb(0 0 0 / 20%), 0 6px 20px 0 rgb(0 0 0 / 19%) !important;
  border-radius: 5px;
  padding: 15px;
  width: 460px;
  overflow: auto;
  height: 320px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  transition: 0s;
  touch-action: none;
  opacity: 0;
  -ms-overflow-style: none;
}
  .popup-window::-webkit-scrollbar {
    display: none;
  }
    .popup-window.is-active {
      opacity: 1;
      pointer-events: all;
      bottom: -10px;
      transition: 0.5s;
    }
.wrapper.popup-window {
    width: 30%;
}
.content-wrapper{
  display: flex;
  margin: 40px;
  flex-direction: column;
}
.input-row{
  display: flex;
  flex-direction: row;
  justify-content: space-between  ;
}
.input-row input{
    padding: 4px;
    height: 20px;
    width: 230px;
    border-left: 1px solid black;
}
.book-button{
  width: 100px;
  height: 24px;
  text-align: center;
  align-self: end;
}
</style>
