<script>
import comp_branch_card from "@/components/branch_intro/comp_branch_card.vue";
export default {
  components: { comp_branch_card },
  data() {
    return {
      branch_info_obj_api: [],
      branch_info_obj: [],
      // branch_info_obj: [
      //   {
      //     name: "桃園中壢車站店",
      //     address: "桃園市中壢區新興路286號",
      //     number: "886-3-4688820",
      //   },
      //   {
      //     name: "台中站前一館",
      //     address: "台中市中區台灣大道一段19號（原中正路11號）",
      //     number: "886-4-22245577",
      //   },
      //   {
      //     name: "台中站前二館",
      //     address: "台中市西區綠川西街29號",
      //     number: "886-4-35067728",
      //   },
      //   {
      //     name: "台中中正店",
      //     address: "台中市中區台灣大道一段441號(原中正路253號)",
      //     number: "886-4-22294466",
      //   },
      //   {
      //     name: "台中成功店",
      //     address: "台中市中區成功路165號",
      //     number: "886-4-35090681",
      //   },
      //   {
      //     name: "台中捷運中清店",
      //     address: "台中市北區華美街二段312號",
      //     number: "886-4-35006537",
      //   },
      //   {
      //     name: "台中捷運文心店(逢甲1號店)",
      //     address: "台中市西屯區文心路三段362號",
      //     number: "886-4-23127766",
      //   },
      //   {
      //     name: "高雄九如店",
      //     address: "高雄市三民區九如一路790號",
      //     number: "886-7-9560604",
      //   },
      //   {
      //     name: "高雄車站店",
      //     address: "高雄市三民區大連街88號",
      //     number: "886-7-3130888",
      //   },
      //]
      inputValue: "",
      branch_info_obj_select: [],
    };
  },

  methods: {
    // 輸入框自動篩選
    auto_select() {
      this.branch_info_obj_select = [];
      this.branch_info_obj = this.branch_info_obj_api;
      // 判斷輸入的內容是否包含items.name
      this.branch_info_obj.forEach((items) => {
        if (
          items.BRANCH_NAMES.includes(this.inputValue) &
          (this.inputValue != "")
        ) {
          this.branch_info_obj_select.push(items);
        }
      });
      // console.log("原始", this.branch_info_obj);
      // console.log("篩選", this.branch_info_obj_select);
      // 保留原始內容，如果有配對到才更新
      if (this.branch_info_obj_select != "") {
        console.log("執行這邊");
        this.branch_info_obj = this.branch_info_obj_select;
        // console.log("branch_info_obj", this.branch_info_obj_api);
      } else {
        this.branch_info_obj = [];
      }
      if (this.inputValue == "") {
        this.branch_info_obj = this.branch_info_obj_api;
      }
    },
    which_branch(element) {
      // 父view傳父view太過複雜，所以把點擊到的對象傳到localstorage，到branch_detail.vue時再讀取顯示
      sessionStorage.setItem("branch_key", element);
    },
  },
  mounted() {
    // 控制footer顯示
    document.querySelector("footer").style.display = "block";
    // 控制後台 nav不顯示
    document.querySelector("#backend_nav").style.display = "none";
    // 取得資料
    fetch("http://localhost:3000/branch")
      .then((response) => {
        console.log(response);
        return response.json();
      })
      .then((data) => {
        console.log(data);
        return (this.branch_info_obj_api = data), (this.branch_info_obj = data);
      })
      .catch((error) => console.error(error));
  },
};
</script>
<template>
  <div>
    <div class="all">
      <div class="title" data-aos="zoom-in-up">分館介紹</div>
      <div class="content">
        奇異果快捷旅店共有9間分館，分布高雄市、台中市以及桃園市。
      </div>
      <div class="search_box" data-aos="zoom-in-up">
        <div class="search_box_t">
          <div class="text">分店名稱查詢 :</div>

          <input
            class="inp"
            ref="inp"
            type="text"
            @keyup="auto_select()"
            v-model="inputValue"
          />
          <button class="search_icon" type="button"></button>
        </div>

        <div class="search_box_b tag_filter">
          <div class="text">縣市 :</div>
          <button type="button" class="tag">桃園市</button>
          <button type="button" class="tag">臺中市</button>
          <button type="button" class="tag">高雄市</button>
        </div>
      </div>
      <div class="cards" data-aos="zoom-in">
        <comp_branch_card
          v-for="item in branch_info_obj"
          :key="item.id"
          @click="which_branch(item.BRANCH_ID)"
        >
          <template v-slot:title>{{ item.BRANCH_NAMES }}</template>
          <template v-slot:tel>{{ item.BRANCH_CONTACT_NUMBER }}</template>
          <template v-slot:address>{{ item.BRANCH_ADRESS }}</template>
        </comp_branch_card>
      </div>
    </div>
  </div>
</template>
<style lang="scss" scoped>
.all {
  @apply bg-[url("@/assets/img/general/img_bg.svg")] bg-no-repeat bg-cover flex flex-col items-center;
  .title {
    @apply flex justify-center text-desktopXlTitle pt-[176px] tracking-widest;
  }
  .content {
    @apply text-desktopBodyText;
  }
  .search_box {
    @apply w-[100%] flex flex-col items-center  mt-[80px];
    .text {
      @apply mr-[6px]  text-desktopTitle;
    }
    .search_box_t {
      @apply flex items-center w-[90%] h-[50px]  text-desktopBodyText;
      .inp {
        @apply w-[20%] h-[100%] border-solid border-[2px] rounded-[12px] pl-[6px];
      }
      .search_icon {
        @apply w-[40px] h-[40px]  bg-[url("@/assets/icons/branch_intro/icon_search.svg")] bg-contain bg-no-repeat bg-bottom;
      }
    }
    .search_box_b {
      @apply flex w-[90%] justify-start text-desktopBodyText mt-[24px];
      .tag {
        @apply px-[6px] py-[4px] mr-[12px] rounded-md   text-baseWhiteColor  bg-SecPinkColor;
      }
    }
  }
  .cards {
    @apply grid grid-cols-3 gap-[10px]  w-[100%] h-[2500px] pt-[35px];
  }
}
</style>
