<template>
  <div class="xggWrap">
    <!-- head -->
    <xggHead :manager='manager'></xggHead>
    <!-- menu -->
    <xggMenu :menucurr='menucurr'></xggMenu>
    
    <!-- main -->
    <div class="xggMain bg-white pb-2 border-left border-bottom">
      <b-breadcrumb :items="items" class="rounded-0 border-bottom py-2 px-4 bg-light"/>
      <div class="container-fluid px-4">
        <h3 class="border-bottom pb-2 mb-5 text-secondary">{{items[items.length-1].text}} <a href="product_group_edit.html" class="btn btn-success float-right px-4">新增</a></h3>
        <div class="py-3 text-center ">
          <table class="table table-bordered">
            <tr class="bg-light">
							<th width="120">编号</th>
							<th>分类名称</th>
							<th>父级分类</th>
							<th>排序</th>
							<th width="150">操作</th>
            </tr>
            <tr v-for='item in product_group'>
            	<td>{{item.id}}</td>
            	<td>{{item.name}}</td>
            	<td>{{item.parent_id}}</td>
            	<td>{{item.sort}}</td>
            	<td><a :href="'product_group_edit.html?id='+item.id">编辑</a> | <span class='btn-link' v-on:click="del(item.id)">删除</span></td>
            </tr>
          </table>
        </div>     
      </div>
    </div>

    <!-- foot -->
    <xggFoot></xggFoot>
		
		<!-- alert -->
		<b-alert class='alert' :variant="alert.type" :show="alert.show">{{alert.msg}}</b-alert>
  </div>
</template>

<style>
@import '../../assets/common.scss';
</style>

<script>

import {_API,config} from '../../config.js'
import xggHead from '../../components/xggHead.vue'
import xggMenu from '../../components/xggMenu.vue'
import xggFoot from '../../components/xggFoot.vue'

export default {
  components: {
    xggHead,
    xggMenu,
    xggFoot
  },
  data () {
    return {
      menucurr:'产品分类',
      items: [{text: '网站管理中心',active: true},{text: '产品分类',active: true}],
      
      manager:{uid:0,uname:''},
			product_group:[{id:0,name:'1',parent_id:'2',sort:'3'}],
			
			alert:{show:false,type:'danger',msg:'这是一个错误提示！'},
    }
  },

  mounted () {
		this.$axios.get(_API+"productgroup")
		.then((res)=>{
			if(res.data.errcode==0){
				this.manager=res.data.data.manager;
				this.product_group=res.data.data.product_group;

			}else if(res.data.errcode==401){
				window.location.href='login.html'; 
			};
		}).catch(function(err){console.log(err);})
		
	},
	methods:{
		timer(n,msg){
			var that=this;
			if(n>0){
				that.alert={show:true,type:'success',msg:msg+'~ '+n+'后自动关闭'};
				setTimeout(function(){that.timer(n-1,msg)},1000);
			}else{
				that.alert.show=false;
			}
		},

		update(id){
			let that=this;
			that.product_group.forEach(function(v,i){
				if(v.id==id){that.product_group.splice(i,1);}
			})
		},

		del(id){
			this.$axios.get(_API+"productgroup/del?id="+id)
			.then((res)=>{
				if(res.data.errcode==0){
					this.timer(3,'删除产品分类成功');
					this.update(id);
				}else if(res.data.errcode==401){
					window.location.href='login.html'; 
				};
			}).catch(function(err){console.log(err);})
		}
	},

};

</script>
