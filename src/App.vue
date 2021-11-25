<template>
  <header class="header">
    <div class="header__logo">Vue Test</div>
    <div class="header__search">
      <form @submit="searching">
        <input v-bind:value="searchString" @input="inputSearch" type="text" class="form-control" placeholder="name package">
        <button type="submit" class="btn btn-primary">Search</button>
      </form>
    </div>
  </header>

  <div class="main-content">
    <div class="container">
      <div class="row">
        <div class="col-12">
          <table class="main-table table table-dark">
            <thead>
            <tr>
              <th><div class="main-table__th">Название пакета</div></th>
              <th><div class="main-table__th">Автор</div></th>
              <th><div class="main-table__th">Версия пакета</div></th>
            </tr>
            </thead>
            <tbody v-for="npm in npms">
            <tr @click="showMore(npm.package)">
              <td><div class="main-table__td">{{npm.package.name}}</div></td>
              <td><div class="main-table__td">{{npm.package.author?.email}}</div></td>
              <td><div class="main-table__td">{{npm.package.version}}</div></td>
            </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>

  <div class="pagination-wrapper">
    <div class="container">
      <div class="row">
        <div class="col-12">
          <ul class="pagination">
            <li
                class="page-item"
                :class="{'active': page === currentPage}"
                v-for="page in pages"
                @click="changePage(page)"
            >
              <a class="page-link" href="#">{{page}}</a>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>

  <footer class="footer">
    <div class="container">
      <div class="row">
        <div class="col-12">
          <a href="https://github.com/biloholovskyi" target="_blank" class="footer__git">github</a>
          <div class="footer__name">Билоголовский Даниил</div>
        </div>
      </div>
    </div>
  </footer>

  <div class="my-modal" v-if="showModal">
    <div class="body">
      <div class="close" @click="closeModal">close</div>
      <div class="name">{{currentNpm?.name}}</div>
      <div class="version">{{currentNpm?.version}}</div>
      <div class="author">
        <div class="author__name">{{currentNpm?.author?.name}}</div>
        <a href="mailto:{{currentNpm?.author?.email}}" class="author__email">{{currentNpm?.author?.email}}</a>
      </div>
      <a href="{{currentNpm?.links?.npm}}" target="_blank" class="link">{{currentNpm?.links?.npm}}</a>
      <div class="desc">{{currentNpm?.description}}</div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      searchString: '',
      npms: [],
      showModal: false,
      currentNpm: null,
      pages: 1,
      currentPage: 1
    }
  },
  methods: {
    async searching(e) {
      e?.preventDefault();

      axios.get('https://registry.npmjs.org/-/v1/search?text=' + this.searchString)
      .then(res => {
        const startIndex = (this.currentPage - 1) * 10;
        const endIndex = startIndex + 10;
        this.npms = res.data.objects.slice(startIndex, endIndex);
        this.pages = Math.ceil(res.data.objects.length / 10);
      })
    },

    inputSearch(e) {
      this.searchString = e.target.value;
    },

    showMore(item) {
      this.currentNpm = item;
      this.showModal = true;
    },

    closeModal() {
      this.showModal = false;
    },

    changePage(pageNumber) {
      this.currentPage = pageNumber;

      this.searching();
    }
  },
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: Roboto, sans-serif;
}

.header {
  display: flex;
  align-items: center;
  padding: 28px 30px;
  background-color: #261d2a;

  &__logo {
    margin-right: 30px;
    color: #fff;
    font-size: 38px;
    font-weight: bold;
    white-space: nowrap;
  }

  &__search {
    width: 100%;

    form {
      width: 100%;
      display: flex;
      align-items: center;
    }

    button {
      margin-left: 20px;
    }
  }
}

.main-content {
  padding: 80px 0;
  min-height: calc(100vh - 113px - 125px - 54px);

  .main-table__td {
    cursor: pointer;
  }
}

.footer {
  padding: 28px 30px;
  background-color: #261d2a;

  &__git {
    color: #fff;
    font-size: 28px;
  }

  &__name {
    color: #fff;
    opacity: .7;
    font-size: 18px;
  }
}

.my-modal {
  position: fixed;
  width: 100vw;
  height: 100vh;
  top: 0;
  left: 0;
  backdrop-filter: blur(5px);
  background: rgba(84, 77, 126, 0.75);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;

  .body {
    background-color: #fff;
    border-radius: 4px;
    padding: 15px;
    position: relative;

    .close {
      position: absolute;
      top: 10px;
      right: 5px;
      cursor: pointer;
    }

    .name {
      font-size: 32px;
      font-weight: bold;
    }

    .version {
      opacity: .7;
      font-size: 18px;
      margin-bottom: 12px;
    }

    .author {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 12px;

      &__name {
        margin-right: 20px;
      }

      &__email {
        color: #111;
      }
    }

    .link {
      opacity: .7;
      color: #111;
    }

    .desc {
      margin-top: 12px;
      opacity: .7;
    }
  }
}
</style>