<template>
    <div class="single-post-page">
<section class="post">
<h1 class="post-title"> {{loadedPost.title}} </h1>
<div class="post-details"> </div>
<div> Last updated on {{loadedPost.updatedDate | date }} </div>
<div>Written by {{loadedPost.author}} </div>
<ul class="list-unstyled">
      <li v-for="(item, index) in exampleData" v-bind:key="index" v-bind:class="[index % 2 == 0 ? 'listRed' : 'listBlue']">{{item}}</li> 
  
 </ul>

</section>
<section class="post-feedback">

    

    <p> Let me know what you think about the post, send a mail to <a href="mailto:ciftcierdinc@gmail.com"> Mail </a> .</p>
</section>
    </div>
</template>

<script>


export default {
  asyncData(context) {
    if (context.payload) {
      return {
        loadedPost: context.payload.postData
      }
    }
      return context.app.$axios.$get('/posts/' + context.params.id + '.json')
      .then(data => {
        return {
          loadedPost: data,
          myLines: data.content.split(/\r\n|\r|\n/),
          otherLines: data.previewText.split(/\r\n|\r|\n/),
        }
      })
      .catch(e => context.error(e))
  },

  head : {
    title: 'A Single Post'
  },

 computed: {
        exampleData: function() {
            let matchedLyrics = [];
          for (let i = 0; i < this.myLines.length; i++) {
                 matchedLyrics.push(this.myLines[i]);
                 try {
                   matchedLyrics.push(this.otherLines[i]);
                 } catch (e) {
                   matchedLyrics.add('-');
                 }
            }
          return matchedLyrics
        }
    },

}




</script>

<style scoped>
.single-post-page {
  padding: 30px;
  text-align: center;
  box-sizing: border-box;
}

.post {
  width: 100%;
}

@media (min-width: 768px) {
  .post {
    width: 600px;
    margin: auto;
  }
}

.post-title {
  margin: 0;
}

.post-details {
  padding: 10px;
  box-sizing: border-box;
  border-bottom: 3px solid #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

@media (min-width: 768px) {
  .post-details {
    flex-direction: row;
  }
}

.post-detail {
  color: rgb(88, 88, 88);
  margin: 0 10px;
}

.post-feedback a {
  color: red;
  text-decoration: none;
}

.post-feedback a:hover,
.post-feedback a:active {
  color: salmon;
}

.pre-formatted {
  white-space: pre;
}

.listRed{
    
  color: #ff0000;
}

.listBlue {
  color: 	#0000FF;
}

</style>



