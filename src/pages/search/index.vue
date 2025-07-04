<template>
  <q-page padding>
    <ais-instant-search :search-client="searchClient" index-name="dev_posts">
      <ais-configure :hits-per-page.camel="8" />
      <div class="main_wrap row q-col-gutter-x-lg">
        <section class="col-3 main_content_wrap q-gutter-y-sm">
          <q-card flat bordered class="q-pa-md">
            <ais-panel>
              <template #header>{{ $t('category') }}</template>
              <template #default>
                <ais-refinement-list
                  :key="locale"
                  attribute="category"
                  :transform-items="translateCategory"
                />
              </template>
            </ais-panel>
          </q-card>
          <q-card flat bordered class="q-pa-md">
            <ais-panel>
              <template #header>{{ $t('tag') }}</template>
              <template #default>
                <ais-refinement-list attribute="tags" />
              </template>
            </ais-panel>
          </q-card>
        </section>
        <section class="col-9 main_content_wrap">
          <ais-search-box class="web_search_box" />
          <q-separator spaced />
          <ais-hits :transform-items="transformItems">
            <template v-slot="{ items }">
              <PostList :items="items" />
            </template>
          </ais-hits>
          <div class="pagination flex flex-center q-mt-md">
            <ais-pagination />
          </div>
        </section>
      </div>
    </ais-instant-search>
  </q-page>
</template>

<script setup>
import { liteClient as algoliasearch } from 'algoliasearch/lite';
import 'instantsearch.css/themes/algolia-min.css';
import PostList from 'src/components/apps/post/PostList.vue';
import { useI18n } from 'vue-i18n';

const { t, locale } = useI18n();

const searchClient = algoliasearch(
  'U6WFDZNP7C',
  '447537de190d0e10c3457713fc71caf8',
);
const transformItems = items => {
  return items.map(item => ({
    id: item.objectID,
    title: item.title,
    content: item._snippetResult.content.value,
    category: item.category,
    tags: item.tags,
    createdAt: item.createdAt,
    readCount: item.readCount,
    likeCount: item.likeCount,
    bookmarkCount: item.bookmarkCount,
    commentCount: item.commentCount,
    uid: item.uid,
  }));
};
const translateCategory = items => {
  return items.map(item => ({
    ...item,
    label: t(`${item.label}`),
  }));
};
</script>

<style lang="scss">
.ais-RefinementList-label {
  display: flex;
  align-items: center;
  width: 100%;
  text-transform: lowercase;
  cursor: pointer;
}
.ais-RefinementList-label > * {
  display: inline-block;
}
.ais-RefinementList-label > .ais-RefinementList-checkbox {
  margin-right: 6px;
}
.ais-RefinementList-label > .ais-RefinementList-labelText {
  flex-grow: 1;
}
.ais-RefinementList-label > .ais-RefinementList-count {
  padding: 0.01rem 0.4rem;
  border-radius: 50%;
}
</style>
