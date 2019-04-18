<template>
  <div class="hello">
    <p style="margin-bottom: 50px">图片拖动排序 -- vue</p>
    <div id="image-display">
      <drag-sort sortid="image-display" v-model="aImage"></drag-sort>  
    </div>
    <p>排序结果：
      <span v-for="item in aImage">{{item.title}}</span>
    </p>
  </div>
</template>

<script>
  import dragSort from './dragSort.vue';
  export default {
    name: 'HelloWorld',
    components: {
      dragSort
    },
    data(){
      return{
        aImage:[
          {
            src: 'https://b-ssl.duitang.com/uploads/item/201706/28/20170628122436_mMBeR.jpeg',
            title: '1'
          },
          {
            src: 'https://b-ssl.duitang.com/uploads/item/201706/28/20170628122432_YiRx4.thumb.700_0.jpeg',
            title: '2'
          },
          {
            src: 'https://b-ssl.duitang.com/uploads/item/201706/28/20170628122425_LtnkF.thumb.700_0.jpeg',
            title: '3'
          },
          {
            src: 'https://b-ssl.duitang.com/uploads/item/201706/28/20170628122422_ctHNr.thumb.700_0.jpeg',
            title: '4'
          },
          {
            src: 'https://b-ssl.duitang.com/uploads/item/201706/28/20170628122409_hn3Md.thumb.700_0.jpeg',
            title: '5'
          },
          {
            src: 'https://b-ssl.duitang.com/uploads/item/201706/28/20170628122412_JhMHF.thumb.700_0.jpeg',
            title: '6'
          },
          {
            src: 'https://b-ssl.duitang.com/uploads/item/201706/28/20170628122415_VFtnM.thumb.700_0.jpeg',
            title: '7'
          },
          {
            src: 'https://b-ssl.duitang.com/uploads/item/201706/28/20170628122419_UB2ri.thumb.700_0.jpeg',
            title: '8'
          },
        ]
      }
    }
  }
</script>