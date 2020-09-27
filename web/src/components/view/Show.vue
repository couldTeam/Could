<template>
  <div class="myfile">
    <el-container
      direction="vertical"
      style="height: 880px; border: 1px solid #eee"
    >
      <head-item></head-item>

      <el-container direction="horizontal">
        <aside-item activeIndex="3"></aside-item>
      </el-container>
      <el-tooltip class="item" effect="dark" :content="showContent" placement="right-start">
        <el-progress
          class="show-circle"
          type="circle"
          :percentage="percent"
        ></el-progress>
      </el-tooltip>
    </el-container>
  </div>
</template>



<script>
import AsideItem from "@/components/AsideItem";
import HeadItem from "@/components/HeadItem";

export default {
  name: "show-space",
  components: {
    AsideItem,
    HeadItem,
  },
  data() {
    return {
      showContent: '',
      sum: '',
      percent: 0,
    };
  },
  created: function () {
    this.requestListApi();
  },
  methods: {
    //获取文件列表
    requestListApi(pageNum, pageSize) {
      var truePageNum = 1;
      var truePageSize = 10;
      var thiz = this;
      this.$http
        .get("/api/usr/file/list", {
          params: {
            pageNum: 1,
            pageSize: 20,
          },
        })
        .then(function (response) {
          if (response.data.code === 200) {
            thiz.loading = false;
            thiz.tableData = response.data.data.list;
            thiz.calSum();
            thiz.totalNumber = response.data.data.totalNumber;
            thiz.pageSize = response.data.data.pageSize;
            thiz.currentPageNum = response.data.data.pageNum;
          } else {
            thiz.showErrorMsg(response.data.msg);
          }
        });
    },
    calSum(){
      var sum = 0;
      var data = new Array();
      this.tableData.forEach(item=>{
        //sum+= this.translate("1GB")
        sum += this.translate(item.size);
        this.sum = this.conver(sum); 
        this.showContent = this.sum+"/1GB"
      });
      this.percent = ((sum / (1024*1024*1024)) *100).toFixed(4);
    },

    translate(size){
      var tmp = size.slice(0,size.length-2);
      console.log(tmp);
      if(size.charAt(size.length-2)==='K') {
          tmp *= 1024;
        }
      else if (size.charAt(size.length-2)==='M'){
          tmp *= 1024;
          tmp *= 1024;
      }
      else if (size.charAt(size.length-2)==='G'){
          tmp *= 1024;
          tmp *= 1024;
          tmp *= 1024;
      }
      else tmp *= 10;
        return tmp;
    },

    conver(limit){  
            var size = "";  
            if( limit < 0.1 * 1024 ){ //如果小于0.1KB转化成B  
                size = limit.toFixed(2) + "B";    
            }else if(limit < 0.1 * 1024 * 1024 ){//如果小于0.1MB转化成KB  
                size = (limit / 1024).toFixed(2) + "KB";              
            }else if(limit < 0.1 * 1024 * 1024 * 1024){ //如果小于0.1GB转化成MB  
                size = (limit / (1024 * 1024)).toFixed(2) + "MB";  
            }else{ //其他转化成GB  
                size = (limit / (1024 * 1024 * 1024)).toFixed(2) + "GB";  
            }  
              
            var sizestr = size + "";   
            var len = sizestr.indexOf("\.");  
            var dec = sizestr.substr(len + 1, 2);  
            if(dec == "00"){//当小数点后为00时 去掉小数部分  
                return sizestr.substring(0,len) + sizestr.substr(len + 3,2);  
            }  
            return sizestr;  
        }  


    
  }
};
</script>

<style scoped>
.show-circle {
  position: absolute;
  left: 300px;
  top: 100px;
}
</style>

