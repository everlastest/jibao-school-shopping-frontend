<template>
  <div id="upload">
    <div class="my" style="position:relative;width:80%;height: 20%; background-color: #faeaea; margin:auto; margin-top: 30px">
      <el-button type="danger" plain
                 style="transform: translateY(8%); position: relative;float: right;z-index: 2;right:8.3%"
                 @click="showuploadcommodity = true">上传
        <i class="el-icon-upload el-icon--right"/>
      </el-button>
      <el-dialog title="上传商品" :visible.sync="showuploadcommodity" @close="showuploadcommodity=false">
        <el-form :rules="rules" ref="commodity" :model="commodity" label-width="80px" class="demo-ruleForm">
          <el-form-item label="商品图片">
            <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :on-preview="handlePictureCardPreview"
              :on-change="handleEditChange"
              list-type="picture-card"
              :auto-upload="false"
              :on-remove="handleRemove"
              :class="{hide:hideUploadEdit}"
            >
              <i class="el-icon-plus"></i>
            </el-upload>
          </el-form-item>
          <el-dialog :visible.sync="dialogVisible" :modal-append-to-body='false'>
            <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
          <el-form-item label="商品名称" prop="name">
            <el-input v-model="commodity.name"></el-input>
          </el-form-item>
          <el-form-item label="商品价格" prop="price">
            <el-input type="number" v-model="commodity.price"></el-input>
          </el-form-item>
          <el-form-item label="商品分类" prop="category" required>
            <el-cascader
              v-model="commodity.category"
              :show-all-levels="false"
              :options="options"
              style="width: 100%"
              clearable></el-cascader>
          </el-form-item>
          <el-form-item label="校区" prop="campus" required>
            <el-select v-model="commodity.campus" placeholder="请选择校区" style="width: 100%">
              <el-option label="四平校区" value="四平校区"></el-option>
              <el-option label="嘉定校区" value="嘉定校区"></el-option>
              <el-option label="沪西校区" value="沪西校区"></el-option>
              <el-option label="彰武路校区" value="彰武路校区"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="商品描述" prop="description">
            <el-input type="textarea" v-model="commodity.description"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitForm('commodity')">上传</el-button>
            <el-button type="danger" @click="showuploadcommodity=false">取消</el-button>
          </el-form-item>
        </el-form>


      </el-dialog>
      <el-tabs type="card" style="" v-model="activeName">
        <el-tab-pane label="在售" name="y">
          <el-empty v-if="onItemList.length===0 " :image-size="300" description="暂无上架物品"></el-empty>
          <div v-for="(item,index) in onItemList" :key="index">
            <el-dialog title="编辑商品" :visible.sync="item.showonload">
              <el-form :rules="rules" ref="temp" :model="item" label-width="80px" class="demo-ruleForm">
                <el-form-item label="商品图片">
                  <el-upload style="margin-left:40px; float: left" action="#" :http-request="requestUpload"
                             :show-file-list="false" :before-upload="beforeUpload">
                    <div class="user-info-head">
                      <img v-bind:src="temp.url" title="点击上传图片"
                           class="img-circle img-lg" alt=""/>
                    </div>
                  </el-upload>
                </el-form-item>
                <el-form-item label="商品名称" prop="name">
                  <el-input v-model="temp.name"></el-input>
                </el-form-item>
                <el-form-item label="商品价格" prop="price">
                  <el-input type="number" v-model="temp.price"></el-input>
                </el-form-item>
                <el-form-item label="商品分类" prop="category" required>
                  <el-cascader
                    v-model="temp.category"
                    :show-all-levels="false"
                    :options="options"
                    clearable></el-cascader>
                </el-form-item>
                <el-form-item label="校区" prop="campus" required>
                  <el-select v-model="temp.campus" placeholder="请选择校区" style="width: 100%">
                    <el-option label="四平校区" value="四平校区"></el-option>
                    <el-option label="嘉定校区" value="嘉定校区"></el-option>
                    <el-option label="沪西校区" value="沪西校区"></el-option>
                    <el-option label="彰武路校区" value="彰武路校区"></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="商品描述" prop="description">
                  <el-input type="textarea" v-model="temp.description"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="submitForm1('temp',item)">修改</el-button>
                  <el-button type="danger" @click="item.showonload=false">取消</el-button>
                </el-form-item>
              </el-form>
            </el-dialog>
            <el-container class="box1" style="min-width: 500px">
              <el-aside class="img1"
                        v-bind:style="{'background':'url('+item.url+')'}"></el-aside>
              <el-main class="text">
                <div style="position: relative;height: 80%;line-height: 25px;">
                  <span style="color: #656565; font-weight: 600">名称：</span>
                  <p style="color: #656565;">{{ item.name }}</p><br>
                  <span style="color: #656565; font-weight: 600">价格：</span>
                  <p style="color: #656565">{{ item.price }}</p><br>
                  <span style="color: #656565; font-weight: 600">分类：</span>
                  <p style="color: #656565;">{{ item.category[0]+'/'+item.category[1]}}</p><br>
                  <span style="color: #656565; font-weight: 600">校区：</span>
                  <p style="color: #656565;">{{ item.campus }}</p><br>
                  <span style="color: #656565; font-weight: 600">描述：</span>
                  <p style="color: #656565;">{{ item.description }}</p><br>
                </div>
                <div style=" position: fixed;height: 42px;right: 20px;">
                  <el-button round icon="el-icon-edit" v-on:click="editItem(item)" type="warning">编辑</el-button>
                  <el-button round icon="el-icon-delete" type="danger" @click="removeItem(item,index)">下架</el-button>
                </div>
              </el-main>
            </el-container>

          </div>
        </el-tab-pane>
        <el-tab-pane label="已卖出" name="n">
          <el-empty v-if="buyItemList.length===0 " :image-size="300" description="暂无卖出物品"></el-empty>
          <el-container class="box1" v-for="(item,index) in buyItemList" :key="index">
            <el-aside class="img1"
                      v-bind:style="{'background':'url('+item.url+')', 'background-repeat':'no-repeat','background-position':'center','background-size':'cover' }"></el-aside>
            <el-main class="text">
              <div style="position: relative;height: 80%;line-height: 30px;">
                <span style="color: #656565; font-weight: 600">商品名称：</span>
                <p style="color: #656565;">{{ item.name }}</p><br>
                <span style="color: #656565; font-weight: 600">买家:</span>
                <p style="color: #656565;">{{ item.customer }}</p><br>
                <span style="color: #656565; font-weight: 600">卖出时间：</span>
                <p style="color: #656565;">{{ item.date }}</p><br>
              </div>
              <div style="position: fixed;height: 42px; right: 20px;">
                <el-button round icon="el-icon-s-custom" type="primary" @click="addUser(item)">买家</el-button>
              </div>
            </el-main>
          </el-container>
        </el-tab-pane>
      </el-tabs>

    </div>
  </div>
</template>

<script>
import {modifyGoods,withdrawGoods, getUpload, uploadGoods} from "../api/goods/goods"
import {getCategory} from "../api/category/category";
import {addChatUser} from "../js/global";
export default {
  name: "upload",
  data() {
    return {
      options: [],
      activeName: 'y',
      formData: new FormData(),
      //修改商品用的
      temp: {
        url: '',
        name: '',
        price: '',
        category: [],
        description: '',
        campus:'',
      },
      onItemList: [],
      buyItemList: [],
      showuploadcommodity: false,
      //上传商品用的
      commodity: {
        image: '',
        name: '',
        price: '',
        category:'',
        description: '',
        campus:'',
      },
      uploadURL: '',
      headerObj: {
        Authorization: localStorage.getItem("token"),
      },
      dialogVisible: false,
      hideUploadEdit: false,
      hideImgEdit: true,
      dialogImageUrl: '',
      limitCount: 1,
      rules: {
        name: [
          {required: true, message: "请输入商品名称", trigger: 'blur'}
        ],
        price: [
          {required: true, message: "请输入商品价格", trigger: 'blur'},
          {pattern: /^(([1-9]{1}\d*)|(0{1}))(\.\d{1,2})?$/, message: "商品价格不合法", trigger: 'blur'}
        ],
        category: [
          {required: true, message: "请输入商品分类", trigger: 'blur'}
        ],
        description: [
          {required: true, message: "请输入商品描述", trigger: 'blur'}
        ],
        campus: [
          {required: true, message: "请输入商品所在校区", trigger: 'blur'}
        ]
      }
    };
  },
  mounted: function () {
    if (!this.$cookies.isKey("user_name")) {
      this.$message({message: "请先登录！", type: 'warning', customClass: 'zZindex'})
      this.$router.push("/Login")
    }
    this.options = []
    getCategory().then(res => {
      console.log(res.data)
      let category = res.data.category
      let length = res.data.category.length
      for (let i = 0; i < length; i++) {
        this.options.push({
          value: category[i].category,
          label: category[i].category,
          children: []
        })
        let s = '';
        for (let j = 0; j < category[i].sub_category.length; j++) {
          if (category[i].sub_category[j] !== '[' && category[i].sub_category[j] !== ']' && category[i].sub_category[j] !== ','&&category[i].sub_category[j] !== ' ') {
            s += category[i].sub_category[j];
          } else if (category[i].sub_category[j] !== '['&&category[i].sub_category[j] !== ' ') {
            this.options[i].children.push({
              value: s,
              label: s,
            })
            s = ''
          }
        }
      }
    }).catch(function (error) {
      console.log(error)
    });
    //得到上架的物品
    getUpload().then(res => {
      this.onItemList=[]
      this.buyItemList=[]
      let goodsList = res.data.goodsInfoList
      console.log(goodsList)
      for (let i=0; i < res.data.goodsInfoList.length; i++) {
        if (goodsList[i].status === "售卖中") {
          this.onItemList.push({
            id: goodsList[i].goods_id,
            name: goodsList[i].goods_name,
            campus:goodsList[i].campus,
            description: goodsList[i].description,
            category: [goodsList[i].category, goodsList[i].sub_category,],
            url: 'http://127.0.0.1:8081/' + goodsList[i].goods_url,
            price: goodsList[i].price / 100,
            showonload: false,
          })
        }
        else if(goodsList[i].status === "已售出"){
          console.log(goodsList[i])
          this.buyItemList.push({
            id:goodsList[i].goods_id,
            price: goodsList[i].price / 100,
            category: [goodsList[i].category,goodsList[i].sub_category,],
            name: goodsList[i].goods_name,
            date: goodsList[i].date,
            campus:goodsList[i].campus,
            url: 'http://127.0.0.1:8081/'+goodsList[i].goods_url,
            customer: goodsList[i].user_name,
            avatar_url: goodsList[i].avatar_url
          })
        }
      }
    }).catch(function (error) {
      console.log(error)
    });
  },
  methods: {
    addUser(item) {
      console.log(item)
      console.log(item.avatar_url)
      addChatUser(item.customer, item.avatar_url)

      this.$router.push('/chat')
    },
    editCropper() {
      this.open = true;
    },
    modalOpened() {
      this.visible = true;
    },
    handleRemove(file, fileList) {
      this.hideUploadEdit = true;
      this.hideUploadEdit = fileList.length >= this.limitCount;
    },
    handlePictureCardPreview(file) {
      console.log(file);
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    handleEditChange(file, fileList) {
      let vm = this;
      vm.hideUploadEdit = fileList.length >= this.limitCount;
      this.formData.append("upload", file.raw)
    },
    // 覆盖默认的上传行为
    requestUpload() {
    },
    // 上传预处理
    beforeUpload(file) {
      if (file.type.indexOf("image/") === -1) {
        this.$message("文件格式错误，请上传图片类型,如：JPG，PNG后缀的文件。");
      } else {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => {
          this.temp.url = reader.result;
        }
        this.formData.append("modify", file)
      }
    },
    //上传商品
    submitForm(commodity) {
      this.$refs[commodity].validate((valid) => {
        if (valid) {
          let image = this.formData.get("upload")
          uploadGoods(this.commodity.description, this.commodity.name, this.commodity.category[1],this.commodity.campus, this.commodity.price * 100, image).then(res => {
            this.$message.success("上传成功！")
            getUpload().then(res => {
              this.onItemList=[]
              this.buyItemList=[]
              let goodsList = res.data.goodsInfoList
              for (let i=0; i < res.data.goodsInfoList.length; i++) {
                if (goodsList[i].status === "售卖中") {
                  this.onItemList.push({
                    id: goodsList[i].goods_id,
                    name: goodsList[i].goods_name,
                    description: goodsList[i].description,
                    campus:goodsList[i].campus,
                    category: [goodsList[i].category, goodsList[i].sub_category,],
                    url: 'http://127.0.0.1:8081/' + goodsList[i].goods_url,
                    price: goodsList[i].price / 100,
                    showonload: false,
                  })
                }
                else if(goodsList[i].status === "已售出"){
                  this.buyItemList.push({
                    id:goodsList[i].goods_id,
                    price: goodsList[i].price / 100,
                    category: [goodsList[i].category,goodsList[i].sub_category,],
                    campus:goodsList[i].campus,
                    name: goodsList[i].goods_name,
                    date: goodsList[i].date,
                    url: 'http://127.0.0.1:8081/'+goodsList[i].goods_url,
                    customer: goodsList[i].user_name,
                  })

                }
              }
            }).catch(function (error) {
              console.log(error)
            });
            this.showuploadcommodity = false
          }).catch(function (error) {
            console.log(error)
          });
        } else {
          console.log('上传失败!!');
          return false;
        }
      });
    },
    //修改商品
    submitForm1(temp,item){
      this.$refs[temp][0].validate((valid) => {
        if (valid) {
          item.description=this.temp.description
          item.name=this.temp.name
          item.price=this.temp.price
          item.campus=this.temp.campus
          item.url=this.temp.url
          item.category=this.temp.category
          let image = this.formData.get("modify")
          modifyGoods(item.id,this.temp.description, this.temp.name, this.temp.category[1],this.temp.campus, this.temp.price * 100, image).then(res => {
            this.$message.success("上传成功！")
            item.showonload = false
          }).catch(function (error) {
            console.log(error)
          });
        } else {
          console.log('上传失败!!');
          return false;
        }
      });
    },
    resetForm(formName) {
      if (formName === 'temp')
        this.showuploadcommodity = false;
      this.$refs[formName].resetFields();
    },

    editItem(item) {
      item.showonload = true
      this.temp.name = item.name
      this.temp.url = item.url
      console.log(item.url)
      this.temp.campus=item.campus
      this.temp.category = item.category
      this.temp.price = item.price
      this.temp.description = item.description
    },
    removeItem(item,index) {
      this.$confirm('确认是否下架?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        setTimeout(() => {
          withdrawGoods(item.id).then(res => {
            let code = res.data.code
            console.log(code)
            if (code === 1)
              this.$message.error("下架失败！")
            else
            {
              this.onItemList.splice(index)
              this.$message.success("下架成功！")
            }

          }).catch(function (error) {
            console.log(error)
          });
        }, 300);
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消下架'
        });
      });
    }
  }
}
</script>

<style>
.my .el-tabs__item {
  color: #656565 !important;
}

.my .el-tabs__item.is-active {
  color: #fcbdab !important;
}

.my .el-tabs__item:hover {
  color: #fcbdab !important;
}

.my .el-tabs__item {

  height: 47px;
  line-height: 47px;
  text-align: center;
  font-size: 16px;
  font-weight: bold;
}

.my .el-tabs__nav-scroll {
  background-color: #f3f4f6;
}

.my .el-tabs--card .el-tabs__header .el-tabs__nav {
  border: none;
}

.el-tabs--card > .el-tabs__header .el-tabs__item.is-active {
  border-bottom-color: #fcbdab;

}

.my .el-tabs__content {
  padding: 10px;
}

.box1 {
  width: 60%;
  background-color: #fff;
  margin-left: 20%;
  margin-right: 20%;
  margin-bottom: 40px;
  height: 250px;
  perspective: 1000px;
  border-radius: 20px;
}

.img1 {
  height: 250px;
  width: 35% !important;
  display: flex;
  flex-direction: column;
  justify-content: center;
  border-radius: 20px 0px 0px 20px;
  background-repeat:no-repeat!important;
  background-position:center!important;
  background-size:cover!important;

}


.text p {
  margin-top: 0px;
  margin-right: 10px;
  word-wrap: break-word;
  white-space: pre-wrap;
  text-align: justify;
  display: inline;
}

.hide .el-upload--picture-card {
  display: none;
}

.user-info-head {
  position: relative;
  display: inline-block;
  height: 120px;
}

.user-info-head:hover:after {
  content: '+';
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  color: #eee;
  background: rgba(0, 0, 0, 0.5);
  font-size: 24px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  cursor: pointer;
  line-height: 110px;
  border-radius: 10px;
}

.img-circle {
  border-radius: 10px;
}

.img-lg {
  width: 120px;
  height: 120px;
}


</style>
