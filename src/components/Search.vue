<template>
  <div class="search-container" >
    <div class="item-detail" v-if="selectedItem">
      <h4>Download Item</h4>
      <i @click.stop ="selectedItem = null">X</i>
      <p>
      <span>Title : {{ selectedItem.metadata.title[0] }}</span>
      <span>Uploaded: {{ selectedItem.metadata.publicdate[0] }}</span>
      </p>
      <ul class="download-list">
        <li v-for="(file, name) in selectedItem.files">
          <p >
            Download {{ file.format }} <button @click.stop="download(name)">Download</button>
          </p>
        </li>
      </ul>
    </div>
    <div class="loading" v-if="loading">Waiting ...</div>
    <input type="text" placeholder="Search" @keyup.enter="onSearch" v-model="searchValue">
    <button @click="onSearch">Search</button>

    <ul class="search-result" v-if="searchResult.length > 0">
      <li v-for="result in searchResult" @click.stop="onItemClick(result.identifier)">
        <h3>{{ result.title }}</h3>
        <h5>
          <span>published date: {{ result.date || 'Not Available' }}</span>
          <span>uploaded date: {{ result.publicdate }}</span>
          <span>Downloaded: {{ result.downloads || '0' }} times</span>
        </h5>
        <p ref="descr">
          description: {{ result.description }}
        </p>
      </li>
    </ul>

  </div>
</template>

<script>
export default {
  name: 'search',
  data() {
    return {
      searchValue: '',
      page: 0,
      searchResult: [],
      loading: false,
      selectedItem: null,
    };
  },
  methods: {
    onSearch() {
      this.loading=true;
      const query = this.searchValue;
      const url = `https://archive.org/advancedsearch.php?q=${query} mediatype:(texts)language:(eng)&fl[]=avg_rating&fl[]=creator&fl[]=date&fl[]=description&fl[]=downloads&fl[]=headerImage&fl[]=identifier&fl[]=language&fl[]=mediatype&fl[]=publicdate&fl[]=publisher&fl[]=title&sort[]=&sort[]=&sort[]=&rows=50&page=1&output=json`;
      this.$http.jsonp(url, {jsonp: 'callback'}).then((response) => {
        this.searchResult = response.body.response.docs;
        this.loading = false;
      });
    },
    onItemClick(id) {
      this.loading = true;
      const detailUrl = `http://archive.org/details/${id}/?output=json`;
      this.$http.jsonp(detailUrl, {jsonp: ''}).then((response) => {
        this.loading = false;
        this.downloadModal = true;
        this.selectedItem = response.body;
      });
    },
    download(identifier) {
      const url = `http://archive.org/download/${this.selectedItem.metadata.identifier[0]}/${identifier}`;
      console.log(url);
      window.location.href = url;
    }
  },
};
</script>

<style lang="scss">
  .loading {
    position: fixed;
    top: 0; bottom: 0; left: 0; right: 0;
    background: rgba(255,255,255,.8);
    z-index: 2;
    text-align: center;
    font-size: 1.3em;
    padding-top: calc(50vh - 3em);
    box-sizing: border-box;
  }
  .search-container {
    background: white;
    width: 85%;
    margin: 0 auto;
    padding: 0 5%;
    box-sizing: border-box;

    input[type="text"] {
      width: 85%;
      height: 45px;
      margin: 20px auto;
      padding: 0 20px;
      box-sizing: border-box;
      font-size: 1.1em;
      line-height: 20px;
      color: #bbb;
      border: 2px solid #bbb;
      border-radius: 5px;
      float: left;

      &:focus {
        color: #555;
        outline: none;
        border-color: #999;
      }
    }

    button {
      border: 0;
      background-color: #3498db;
      box-sizing: border-box;
      display: block;
      margin-left: 20px;
      margin-top: 20px;
      height: 45px; width: 100px;
      padding: 4px 10px;
      float: left;
      font-size: 1.15em;
      color: #fff;
      border-radius: 2px;
      cursor: pointer;

      &:hover {
        background: darken(#3498db, 7%);
      }
      &:active {
        background: darken(#3498db, 12%);
      }
      &:after {
        content: ' ';
        display: block;
        clear: both;
      }
    }

    .download-list {
      position: relative;
      overflow: auto;

      p{
        margin-top: 50px;
        button {
          font-size:0.8em;
          padding: 2px 3px;
          height: 30px; width: 80px;
          float: right;
          position: relative;
          top: -30px;
          clear:right;
        }
      }
    }

    .search-result {
      display: block;
      width: 100%;
      list-style: none;
      clear: both;
      text-align: left;
      position: relative;
      top: 50px;
      border: 1px solid #aaa;
      border-radius: 5px;
      box-sizing: border-box;
      li {
        display: block;
        padding: 50px 30px;
        border-bottom: 1px solid #ddd;
        cursor: pointer;

        &:hover {
          background: darken(#fff,1%);
        }

        h3 {
          font-size: 1.5em;
          font-weight: bold;
        }
        h5 {
          color: #bbb;
          margin: 10px 0px;

          span {
            margin-right: 20px;
            font-size: 0.95em;
          }
        }

        p {
          font-size: 0.9em;
          line-height: 22px;
        }
      }
    }
  }

  .item-detail {
    position: fixed;
    width: 85%;
    height: 50vh;
    box-sizing: border-box;
    background-color: #fff;
    padding: 10px 40px;
    border: 2px solid rgba(210,210,210, .8);
    left: 50%;
    margin-left: -42.5%;
    color: #000;
    z-index: 100000;

    i {
      display: block;
      position: absolute;
      top: 10px; right: 20px;
      font-size: 1.2em;
      cursor: pointer;
      z-index: 100000;
    }

    h4 {
      font-size: 1.4em;
      font-weight: bold;
    }

    p {
      text-align: left;
    }

    li{
      margin-top: 15px;
    }
    .download-list {
      height: 200px;
      overflow-y: scroll;
    }
  }
</style>
