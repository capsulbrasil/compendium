---
author: João G. Santos (@ringeringeraja)
tags: vue reactivity
---

# Sharing reactivity without using a Global State Manager (Vuex, Pinia)

Let's take the `sv-topbar` component as an example. It has a searching widget inside it that for layouting purposes had to be split in two different sub-components. So its inner directory structure is as following:

While `sv-search-bar` consists of an input to search for data across all collections, `sv-search-results` is intended for displaying these data, so, for making it clear, both components must share data among them, most specifically reactive data.

Whereas registering a Pinia store just for this is tempting, this approach would polute the application structure with a whole store intended for a single sub-component. So, a event-listener approach to transit the data from first to parent then from parent to the second component, you may ask? That sounds painful and you'll would probably be fired if you came up with this.

Every scenarios considered the better solution would be still maintain the reactive object inside its context in the filesystem, so a `stores/search.ts` file is created within the `_internals` where the components are located in. Inside it we export our objects using Vue native `reactive` function, or `ref` function if it suits it better (`ref` is is often used to store literals) so it can be imported by both `sv-search-bar` and `sv-search-results`. The final result looks like below:

Object `results` is exported by `stores/search.ts`:
```typescript
import { reactive } from 'vue'

export const results = reactive({
  items: []
})
```

Object `results` is imported then a HTTP request response is written on it in `sv-search-bar.vue`:
```vue
<script setup lang="ts">
import { ref, watch } from 'vue'
import { useHttp } from '@savitri/web'
import { results } from '../../stores/search'

const { http } = useHttp()
const query = ref('')

watch(() => query.value, async () => {
  if( query.value.length === 0 ) {
    results.items = []
    return
  }

  // "searchable" is a controllable, so the route starts with "_"
  results.items = (await http.post('_/searchable/search', {
    query: query.value
  })).data.result
})
</script>
```

Then the results are imported then read in `sv-seasrch-results.vue`:
```vue
<script setup lang="ts">
import { computed } from 'vue'
import { results } from '../../stores/search'

console.log(results)
</script>
```

Directory structure:
```
sv-topbar
├── _internals
│   ├── components
│   │   ├── sv-search-bar
│   │   │   ├── sv-search-bar.scss
│   │   │   └── sv-search-bar.vue
│   │   └── sv-search-results
│   │       ├── sv-search-results.scss
│   │       └── sv-search-results.vue
│   └── stores
│       └── search.ts
├── sv-topbar.scss
└── sv-topbar.vue

```
