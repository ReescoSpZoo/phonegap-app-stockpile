<template>
  <f7-page name="details" @page:beforeanimation="onPageBeforeAnimation">
    <f7-navbar back-link="Back" sliding>
      <f7-nav-center>
        Details
      </f7-nav-center>
    </f7-navbar>
    <!-- Scrollable page content-->
    <f7-card>
      <f7-card-header>
        <div class="img-container" :style="imgBackground()"
          @click="loadInPhotoBrowser">
          <div class="img-container-inner" :style="imgBackground(500)"></div>
          <div class="caption">{{item.title}}</div>
        </div>
      </f7-card-header>
      <f7-card-content>
        <f7-list>
          <f7-list-item title="Category" :after="item.category.name"
            :link="categoryLink"
          ></f7-list-item>
          <f7-list-item
            title="Created by" :after="item.creator_name" :link="creatorLink"
          ></f7-list-item>
          <f7-list-item
            title="Creation date" :after="creationDate"></f7-list-item>
        </f7-list>
      </f7-card-content>
      <f7-card-footer>
        <f7-link :href="item.comp_url" external>Download Comp</f7-link>
        <f7-link :href="findMoreLink">Find Similar</f7-link>
      </f7-card-footer>
    </f7-card>
    <f7-photo-browser
      ref="pb"
      type="page"
      :photos="photos"
      :lazyLoading="true"
      backLinkText="Details"
      :toolbar="false">
    </f7-photo-browser>
  </f7-page>
</template>

<script>
  /* global store */
  import moment from 'moment';

  export default {
    name: 'Details',
    data () {
      return store;
    },
    computed: {
      id () {
        const { id } = this.$route.params;
        return id;
      },
      item () {
        if (this.imagesById && this.imagesById[this.id]) {
          this.stockItem = Object.assign(
            {},
            this.stockItem,
            this.imagesById[this.id]);
        }
        return this.stockItem;
      },
      photos () {
        return [
          {
            url: this.item.thumbnail_1000_url,
            caption: this.item.title || ''
          }
        ];
      },
      creationDate () {
        const created = moment(this.item.creation_date);
        return created.format('MMMM Do YYYY');
      },
      categoryLink () {
        return `/results/category/60/${this.item.category.id}/details`;
      },
      creatorLink () {
        return `/results/creator_id/60/${this.item.creator_id}/details`;
      },
      findMoreLink () {
        return `/results/similar/60/${this.item.id}/details`;
      }
    },
    methods: {
      loadInPhotoBrowser () {
        this.$refs.pb.open();
      },
      onPageBeforeAnimation () {
        // When going 'back' from the photo browser, make sure we disable
        // exposition (hidden navbar, etc) if it was enabled
        const { pb: photoBrowser } = this.$refs;
        if (photoBrowser.f7PhotoBrowser.exposed) {
          photoBrowser.disableExposition();
        }
      },
      imgBackground (size = 0) {
        const url = size > 0 ? `thumbnail_${size}_url` : 'thumbnail_url';
        if (this.item[url]) this[url] = this.item[url];
        return `background-image: url(${this[url]})`;
      }
    }
  };
</script>

<style scoped>
  .swiper {
    height: 300px;
  }
  .img-container {
    position: relative;
    width: 100%;
    height: 240px;
    max-height: 240px;
    overflow: hidden;
    margin: 0;
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center center;
  }
  .img-container-inner {
    position:absolute;
    top: 50%;
    transform: translateY(-50%);
    -webkit-transform: translateY(-50%);
    width: 100%;
    height: 100%;
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center center;
    z-index: 2;
  }
  .caption {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: rgba(0,0,0,0.3);
    color: white;
    padding: 8px 16px;
    z-index: 3;
  }
</style>
