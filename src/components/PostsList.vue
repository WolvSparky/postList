<template>
  <div class="PostsList">
    <ul>
      <li v-for="item in items" :key="item.id">
        {{ item.title }}
      </li>
    </ul>
    <div v-show="prevPageToken.seen()" class="prev">{{ prevPageToken.value }}</div>
    <div v-show="nextPageToken.seen()" class="next">{{ nextPageToken.value }}</div>
    
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "PostsList",
  data() {
    return {
      items: [],
      actuallyToken: 'Not exists',
      prevPageToken: {
        value: 'Prev page',
        token: null,
        seen() {
          if (this.token != 'Prev page') {
            return true;
          } else {
            return false;
          }
        }
      },
      nextPageToken: {
        value: 'Next page',
        token() {
          if(this.actuallyToken === 'Not exists') {
            return null;
          }
        }, 
        seen() {
          if (this.token != 'Next page') {
            return true;
          } else {
            return false;
          }
        }
      },
      url() {
        if(this.nextPageToken.token() != null) {
          return 'https://www.googleapis.com/blogger/v3/blogs/2399953/posts?key=AIzaSyCUOBRJiCdis1rmJ8Lz8IfETv2KDTvKv28&pageToken='+this.nextPageToken.token;
        } else {
          return 'https://www.googleapis.com/blogger/v3/blogs/2399953/posts?key=AIzaSyCUOBRJiCdis1rmJ8Lz8IfETv2KDTvKv28';
        }
      },
    };
  },
  mounted () {
    axios
      .get(this.url())
      .then(response => {
        if(response) {
          this.items = response.data.items;
          
          if(this.actuallyToken != this.nextPageToken.token && this.nextPageToken.token != 'Next page') {
            this.prevPageToken.token = this.actuallyToken;
            this.actuallyToken = response.data.nextPageToken;
            console.log(this.actuallyToken);
          }
          if(response.data.nextPageToken) {
            this.nextPageToken.token = response.data.nextPageToken;
          }
        } else {
          throw new Error('Request failed!');
        }
      })
  },
  methods: {

  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul {
  margin: 0;
  padding: 0;
  list-style-type: none;
}
li {
  width: 100%;
  margin: 0;
  margin-top: 5vh;
  background: rgba(0, 0, 0, .3);
  padding: 2.5vh;
  h1 {
    font-size: 15px;
    padding: 0;
    margin: 0;
  }
}

.next, .prev {
  position: absolute;
  background: red;
  width: 46%;
  height: 20vh;
  margin-top: 5vh;
  color: white;
  line-height: 20vh;
}

.next {
  right: 0;

  &:hover {
    background: #dd0000;
    cursor: pointer;
  }
}
</style>
