<template>
  <div class="ticket-manage">
    <div class="ticket-manage-header">
      <div class="header-left">
        <el-input
          v-model="searchValue"
          class="w-200 m-2"
          size="large"
          :placeholder="$t('Search')"
          :suffix-icon="Search"
        />
        <div class="container-radio">
          <el-radio-group v-model="filterTicket">
            <el-radio-button label="0">{{ $t('All') }}</el-radio-button>
            <el-radio-button label="1">{{ $t('VIP') }}</el-radio-button>
            <el-radio-button label="2">{{ $t('Normal') }}</el-radio-button>
          </el-radio-group>
        </div>
      </div>
    </div>
    <div class="ticket-manage-main">
      <section class="pay-done">
  <h1 class="pay-done__head reddish">{{ Result.Message }}</h1>
  <div class="pay-done__icon"> <i class="fa fa-check reddish"></i></div>
   <p class="pay-done__thank">Thanh toán đã thực hiện thành công. Trong vòng 30 phút Shopdunk sẽ liên hệ xác nhận thông tin giao hàng cho quý khách.</p>
  <ul class="pay-done__info">
    <li class="pay-done__info__row">
      <p class="pay-done__info__row__name">Hình thức thanh toán </p> 
      <p class="pay-done__info__row__detail">{{ Result.vnp_BankCode }}</p>
    </li>
    <li class="pay-done__info__row">
      <p class="pay-done__info__row__name">Email nhận vé </p> 
      <p class="pay-done__info__row__detail">tmhiep@gmail.com</p>
    </li>
    <li class="pay-done__info__row">
      <p class="pay-done__info__row__name"> Số điện thoại </p> 
      <p class="pay-done__info__row__detail">0123456789</p>
    </li>
     <li class="pay-done__info__row cash">
      <p class="pay-done__info__row__name  "> Số tiền</p> 
      <p class="pay-done__info__row__detail">{{ formatCurrency(Result.vnp_Amount) }}</p>
    </li>
     <li class="pay-done__info__row">
      <p class="pay-done__info__row__name"> Mã giao dịch </p> 
      <p class="pay-done__info__row__detail">{{Result.vnp_TransactionNo}}</p>
    </li>
  </ul>
  <div class="pay-done__button"> <button>Tiếp tục đặt vé</button></div>
 
  <p>Mọi thắc mắc, hỗ trợ vui lòng gọi  <span class="pay-done__num">0981.166.642</span> hoặc <span class="pay-done__num">1900.6626 </span> (7h30 - 22h). Cám ơn quý khách đã cho Shopdunk cơ hội phục vụ!</p>
</section>
    </div>
    <div class="ticket-manage-footer"></div>
  </div>
</template>
<script>
import VsudInput from "../components/VsudInput.vue";
import { convertDateFormat,formatNumber } from "@/common/commonFunc";
import BaseButton from "./components/BaseButton.vue";
import { useRoute } from 'vue-router';
import {
  Check,
  Delete,
  Edit,
  Message,
  Search,
  Star,
} from "@element-plus/icons-vue";
export default {
  components: { VsudInput, BaseButton },
  setup() {
    return {
      convertDateFormat,Search,formatNumber
    };
  },
  created() {
    const route = useRoute();

    // Lấy giá trị các tham số từ URL
    const vnp_Amount = route.query.vnp_Amount;
    const vnp_BankCode = route.query.vnp_BankCode;
    const vnp_ResponseCode = route.query.vnp_ResponseCode;
    const vnp_TransactionNo = route.query.vnp_TransactionNo;
    const Status =route.query.Status; //0: Cho thanh toan,1: da thanh toan,2: GD loi
    const vnp_TransactionStatus = route.query.vnp_TransactionStatus;

     this.Result = {
      vnp_Amount:vnp_Amount,
      vnp_BankCode:vnp_BankCode,
       vnp_ResponseCode : route.query.vnp_ResponseCode,
     vnp_TransactionNo : route.query.vnp_TransactionNo,
     Status :route.query.Status, //0: Cho thanh toan,1: da thanh toan,2: GD loi
     vnp_TransactionStatus : route.query.vnp_TransactionStatus,
     vnp_OrderInfo : route.query.vnp_OrderInfo
    }
    console.log(this.Result);
    // ... và các tham số khác
    console.log("giá trị trả về" , vnp_BankCode);
    let me = this;
    this.$store.state.isShowLoading = true;
    // nếu thành công thì update vé đã thanh toán và cập nhật ghế
    if(this.Result.vnp_TransactionStatus == "00")
    {
      this.$api.post("/History/UpdateStatusInHistorySuscess",{orderId:this.Result.vnp_OrderInfo}).then((data) => {
      // me.listTicket = data;
      var seatsSelecting = localStorage.getItem('seatsSelecting');
      console.log("seat update",seatsSelecting);
      this.$api
        .post("/Movie/UpdateSeatRoomCinema", seatsSelecting)
        .then(() => {
          me.$store.dispatch("showToast", this.$t("Ticketbookingsuccessful"));
          this.Result.Message = "Thanh toán thành công!";
        });

      this.$store.state.isShowLoading = false;
    });
    }
    else{
      this.Result.Message = "Thanh toán không thành công!";
    }
    // this.$api.post("/Ticket/GetListTemplateTicket",{}).then((data) => {
    //   me.listTicket = data;
    //   this.$store.state.isShowLoading = false;
    // });
  },
  mounted() {},
  data() {
    return {
      listTicket: [],
      ticketSelected: "",
      costSelected: 0,
      searchValue: "",
      filterTicket: "0",
      Result:{
        Message:"Chưa thanh toán",
        vnp_BankCode:'',
        vnp_ResponseCode:'',
        vnp_TransactionStatus:''

      }
    };
  },
  methods: {
    showChangeMoney(id, cost) {
      this.ticketSelected = id;
      this.costSelected = cost;
    },
    formatCurrency(amount) {
      amount = amount / 100;
      const formattedAmount = amount.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
      return `${formattedAmount} VNĐ`;
  },
    isFilterItem(item) {
      if ((this.filterTicket == "0")) {
        return true;
      }

      if ((this.filterTicket == "1" && item.type != 1)) {
        return true;
      }

      if ((this.filterTicket =="2" && item.type == 1)) {
        return true;
      } else {
        return false;
      }
    },

    isShowTicket(item) {
      return (
        item.movieName.toLowerCase().includes(this.searchValue.toLowerCase()) ||
        item.movieCode.toLowerCase().includes(this.searchValue.toLowerCase())
      );
    },

    isShowingChange(id) {
      if (this.ticketSelected == id) {
        return true;
      } else {
        return false;
      }
    },

    loadData() {
      let me = this;
      this.$store.state.isShowLoading = true;

      this.$api.post("/Ticket/GetListTemplateTicket",{}).then((data) => {
        me.listTicket = data;
        this.$store.state.isShowLoading = false;
      });
    },

    saveChangeMoney(id, cost) {
      let me = this;
      this.$api
        .post("/Ticket/UpdateCostOfTicket", {
          templateTicketID: id,
          cost: cost,
        })
        .then((data) => {
          if (data) {
            me.ticketSelected = "";
            me.loadData();
            me.$store.dispatch("showToast", this.$t('UpdateSuccessful'));

          } else {
            me.$store.dispatch(
              "showToast",
              this.$t('Updatefailedpleasetryagain')
            );
          }
        });
    },
  },
};
</script>
<style lang="scss">
.ticket-manage {
  padding: 30px 28px 0;
  .ticket-manage-header {
    height: 60px;
    display: flex;
    justify-content: space-between;
    padding: 0 20px;
    box-shadow: rgba(50, 50, 93, 0.25) 0px 2px 5px -1px,
      rgba(0, 0, 0, 0.3) 0px 1px 3px -1px;
    background: #fff;
    border-radius: 10px;
    align-items: center;

    .form-group {
      margin-bottom: 0px !important;
    }

    .header-left {
      display: flex;
      align-items: center;
      .filter-movie {
        height: 36px;
        margin-left: 10px;
      }
    }
    label.el-radio-button {
      margin: 0px !important;
    }

    .container-radio {
      height: 40px;
      display: flex;
      align-items: center;
      margin-left: 10px;
    }
  }

  .ticket-manage-main {
    box-shadow: rgba(50, 50, 93, 0.25) 0px 2px 5px -1px,
      rgba(0, 0, 0, 0.3) 0px 1px 3px -1px;
    background: #fff;
    border-radius: 10px;
    padding: 20px 0;
    margin-top: 30px;
    display: flex;
    flex-wrap: wrap;
    min-width: 500px;
    .ticket-container {
      margin-left: 20px;
      margin-bottom: 20px;
      height: 400px;
      width: 250px;
      box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
      .ticket-item {
        color: #111;
        height: 100%;
        width: 100%;
        padding: 10px;
        background: #ded1d8;
        font-size: 13px;
        .name-cinema {
          font-weight: 600;
          font-size: 14px;
        }
        .about {
          font-size: 12px;
          font-style: italic;
        }

        .border-dash {
          height: 1px;
          border-top: 1px dashed #111;
          margin: 3px 0px 10px 0;
        }
        .item-main {
          .name-movie {
            font-weight: 600;
            font-size: 18px;
          }
        }

        .item-footer {
          .cost-ticket {
            outline: none !important;
          }
        }

        .item-feature {
          height: 80px;
          align-items: center;
          display: flex;
          justify-content: center;
          .item-changing {
            width: 180px;
            display: flex;
            justify-content: space-evenly;
          }
        }
      }
    }
  }
}
.reddish{
  color: #b10d0d;
}
.pay-done{
  box-shadow: rgba(0, 0, 0, 0.2) 0px 0px 50px 5px;
    min-height: 500px;
    max-width: 700px;
    margin: auto;
    padding: 50px;
    font-family: "Open Sans", sans-serif;
    font-size: 13px;
    color: rgba(0, 0, 0, 0.7);
    line-height: 1.7;
 
}

.pay-done__head{

  font-family: 'Muli', sans-serif;
  font-weight:300;
  font-size: 25px;
  text-align:center;
}

.pay-done__thank{
  margin-top: 50px;
 
}
.pay-done__icon{
  display:flex;
}
.pay-done__icon .fa{
border: solid;
  border-radius: 50%;
  padding: 10px;
 font-size: 25px;
  margin:auto;
}

.pay-done__info{
 margin-top: 35px;

}

ul{
  padding-inline-start: 0px;
}

.pay-done__info__row{
  display:flex;  
  justify-content:space-between;
  line-height: 0px;
    margin-block-start:0px; 
}


.pay-done__info__row__name{
 color: rgba(0,0,0,0.5);
  font-weight:600;
}
.pay-done__info__row__detail{
 color: rgba(0,0,0,0.7);
  font-weight:600;
  
}
.pay-done__info__row.cash{
  

  line-height: 1.4;
  }

.pay-done__info__row.cash{
  margin-top: 10px;
}
.pay-done__info__row.cash .pay-done__info__row__name{
  font-weight: 900;
  color: rgba(0,0,0,0.9);
  
}
.pay-done__info__row.cash .pay-done__info__row__detail{
  font-weight: 800;
 font-size: 15px;
  color:black;
  }

.pay-done__num{
  font-weight: bold;
}
.pay-done__button{
 margin: 40px 0 80px 0;
  display:flex;
  position:relative;
}
.pay-done__button button{
  background: #b10d0d;
  position:absolute;
  right:0;
  padding: 10px;
  color:white;
  text-transform:uppercase;
  font-weight:bold;
  border:none;
  box-shadow: #eac1c1 3px 5px 20px 1px;
}

@media screen and (max-width: 600px){
  .pay-done__info__row{
    flex-direction: column;
    }
  .pay-done{
    padding: 20px;
  }
  .pay-done__button{
    margin:  20px 0px 20px 0px;
    
  }
  .pay-done__button button{
    left:0;
    position:static;
  }
}
</style>
