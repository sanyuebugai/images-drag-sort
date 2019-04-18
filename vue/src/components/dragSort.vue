<template>
	<div>
		<div 
			draggable="true" 
			@dragstart="drag(item,index,$event)" 
			class="image-display-box" 
			v-for="(item,index) in value"
			:key="index"
			:class="{'sorting':bSort,'sortItem':item==sortItem}"
		>
			<div>
				<img :src="item.src">
				<div class="move"></div>
				<p class="mask">
					<span @click="delHandle(item)">删除图片</span>
				</p>
			</div>
			<p class="title">{{ item.title }}</p>
		</div>
	</div>
</template>
<script>
	export default{
		name: 'dragSort',
		props: {
			sortid: String,
			value: Array
		},
		data(){
			return{
				aImage: this.value,

				bSort: false,
    		sortItem: null,
    		sortItemIndex: 0,
    		sortInfo: {
					relySrc: '',//排序参考的那个图片的src
					type: 'left'//left or right
				}
			}
		},
		watch:{
			value(val){
				this.aImage = val;
			}
		},
		methods:{
			drag(item, index, event){//拖动事件
				event.preventDefault();
				this.bSort = true;
				this.sortItem = item;
				this.sortItemIndex = index;
      },  
			delHandle(item){
				this.aImage.splice($.inArray(item, this.aImage) ,1);
			},
			sort(){
      	// 拖动排序部分功能实现
				let self = this;
				self.$nextTick(()=>{
					var mousePos = {};
					function mousePosition(ev){
						if(ev.pageX || ev.pageY){
							return {x:ev.pageX, y:ev.pageY};
						}
						return {
							x:ev.clientX + document.body.scrollLeft - document.body.clientLeft,
							y:ev.clientY + document.body.scrollTop - document.body.clientTop
						};
					}
					document.onmousemove = mouseMove;
					document.onmouseup = mouseUp;
					function mouseMove(ev){
					  ev = ev || window.event;
					  mousePos = mousePosition(ev);
					  var imageDisplay = $('#' + self.sortid)[0].getBoundingClientRect();
					  var poi = [];
					  if(self.bSort){
					  	var len = $('.sorting').length;
					  	for(var i = 0; i < len; i++){
					  		var o = $('.sorting')[i].getBoundingClientRect();
					  		poi.push(o);
					  	}
						  if( mousePos.x < imageDisplay.left + imageDisplay.width 
						  	  && mousePos.x > imageDisplay.left
						  	  && mousePos.y < imageDisplay.top + imageDisplay.height
						  	  && mousePos.y > imageDisplay.top ) {
						  	// console.log(mousePos)
						  	poi.forEach((i,index)=>{
						  		if( mousePos.x < i.left + i.width 
								  	  && mousePos.x > i.left
								  	  && mousePos.y < i.top + i.height
								  	  && mousePos.y > i.top
								  	  && index != self.sortItemIndex){
						  			if( mousePos.x < i.left + (i.width)/2){
						  				// 目标图左半部分
						  				// 插入一个竖线标识
						  				$($('.image-display-box')[index]).addClass('sortleft').removeClass('sortright');
						  				self.sortInfo = {
						  					relySrc: $($('.image-display-box')[index]).find('img').attr('src'),
						  					type: 'left' 
						  				}
						  			} else if( mousePos.x > i.left + (i.width)/2){
						  				// 目标图右半部分
						  				// 插入一个竖线标识
						  				$($('.image-display-box')[index]).addClass('sortright').removeClass('sortleft');
						  				self.sortInfo = {
						  					relySrc: $($('.image-display-box')[index]).find('img').attr('src'),
						  					type: 'right'
						  				}
						  			}
						  	  } else{
										$($('.image-display-box')[index]).removeClass('sortleft').removeClass('sortright');
									}
						  	})
						  }
					  }
					}
					function mouseUp(ev){
						ev = ev || window.event;
						$('.image-display-box').removeClass('sortleft').removeClass('sortright');
						// 处理数组
						if(self.bSort && self.sortInfo.relySrc!=''){
							self.aImage.splice($.inArray(self.sortItem, self.aImage),1);
							let flg = false;
							self.aImage.forEach((i,index)=>{
								if(i.src == self.sortInfo.relySrc && !flg){
									flg = true;
									if(self.sortInfo.type == 'left'){
										self.aImage.splice(index, 0, self.sortItem)
									} else if(self.sortInfo.type == 'right'){
										self.aImage.splice(index + 1, 0, self.sortItem)
									}
								}
							})
						}
						self.$emit('input', self.aImage)
						self.bSort = false;
						self.sortItem = null;
						self.sortItemIndex = 0;
						self.sortInfo = {
							relySrc: '',//排序参考的那个图片的src
							type: 'left'//left or right
						}
					}
				})
      }  
		},
		created(){
			this.aImage = this.value;
			this.sort();
		},
		beforeDestroy (){
      document.onmousemove = null;
			document.onmouseup = null;
    },
    deactivated (){
      document.onmousemove = null;
			document.onmouseup = null;
    }
	}
</script>
<style>
	.image-display-box{
		position: relative;
		display: inline-block;
		margin-bottom: 10px;
		margin-right: 10px;
	}
	.image-display-box div{
		position: relative;
		width: 270px;
		height: 160px;
		border: 1px solid #d2d6de;
		cursor: move;
	}
	.image-display-box div img{
		display: block;
		margin: 0 auto;
		width: auto;
		height: auto;
		max-height: 158px;
		max-width: 268px;
	}
	.image-display-box div div.move{
		opacity: 0;
		position: absolute;
    z-index: 22;
    top: 0;
    left: 0;
    width: 100%;
    height: 160px;
    border: none;
    background-color: rgba(222, 222, 222, 0.35);
	}
	.image-display-box div p.mask{
		cursor: default;
    opacity: 0;
    position: absolute;
    z-index: 22;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 40px;
    background-color: rgba(74, 74, 74, .8);
    margin-bottom: 0;
    text-align: center;
    padding-top: 10px;
		-webkit-transition: all .4s ease;
		   -moz-transition: all .4s ease;
		    -ms-transition: all .4s ease;
		     -o-transition: all .4s ease;
		        transition: all .4s ease;
	}
	.image-display-box div p.mask span{
		background-color: #de2619;
    color: #fff;
    padding: 5px 10px;
    border-radius: 5px;
	}
	.image-display-box div p.mask i{
    cursor: pointer;
    color: #e0e0e0;
    position: absolute;
    top: 5px;
    right: 5px;
    font-size: 22px;
	}
	.image-display-box div:hover p.mask{
		opacity: 1;
	}
	p.title{
    font-size: 12px;
    margin-bottom: 0;
    line-height: 18px;
    width: 270px;
    overflow-x: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
	}
	.image-display-box.sortItem div.move{
		opacity: 1;
	}
	.image-display-box:before,
	.image-display-box:after{
		content: ' ';
		width: 5px;
		height: 160px;
		background-color: #50bfff;
		position: absolute;
	}
	.image-display-box:before{
		opacity: 0;
		left: -7px;
		top: 0;
	}
	.image-display-box:after{
		opacity: 0;
		right: -7px;
		top: 0;
	}		
	.image-display-box.sorting:hover p.mask{
		opacity: 0;
	}
	.image-display-box.sortleft:before{
		opacity: 1;
	}
	.image-display-box.sortright:after{
		opacity: 1;
	}
</style>