# Nuxt.Js Help You

## About Axios

##### Axios হল একটি জনপ্রিয় HTTP ক্লায়েন্ট যা API থেকে ডাটা ফেচ করার জন্য ব্যবহার করা হয়। Nuxt.js এ Axios ব্যবহার করা খুবই সহজ, এবং এটি SSR (Server-Side Rendering) এবং CSR (Client-Side Rendering) উভয় ক্ষেত্রেই কাজ করে।

#### For Install axios you please command

```
npm install @nuxtjs/axios
```
### After Install axios then axios set in Nuxt Config

```
export default defineNuxtConfig({
  modules: ['@nuxtjs/axios'],
  axios: {
    baseURL: 'https://jsonplaceholder.typicode.com', // ডিফল্ট API URL
  }
});

```
*If you set the `baseURL` here, it will be used every time an API call is made.

### API call using Axios

```
const { $axios } = useNuxtApp();
const posts = ref([]);
async () => {
  try {
    const response = await $axios.get('/posts');
    posts.value = response.data;
  } catch (error) {
    console.error('Error fetching posts:', error);
  }
}
```

*If you use `useNuxtApp().$axios`, the `baseURL` provided in `nuxt.config.ts` will be automatically included.

### Handling Axios with a composable file (Best Practice)

#### Nuxt 3 Composable - `useApi.ts`

Nuxt 3 এ Composable ফাইল ব্যবহার করলে কোড ক্লিন এবং রিপিটিশন কম হয়।

## ✅ একটি নতুন `useApi.ts` Composable তৈরি করুন:

```
export default function useApi() {
  const { $axios } = useNuxtApp();

  const getPosts = async () => {
    try {
      const response = await $axios.get('/posts');
      return response.data;
    } catch (error) {
      console.error('Error fetching posts:', error);
      return [];
    }
  };

  return { getPosts };
}
```

✅ এখন Vue কম্পোনেন্টে ব্যবহার করুন:

```
import useApi from '@/composables/useApi';

const { getPosts } = useApi();
const posts = ref([]);

onMounted(async () => {
  posts.value = await getPosts();
});



```

✅ **Axios** ব্যবহার করলে `useNuxtApp().$axios` দিয়ে API কল করা যায়।  
✅ **Composable (useApi.ts)** ব্যবহার করলে কোড আরও ক্লিন হয়।


============ UseFetch()=========
##### useFetch() হল Nuxt.js এ data fetching করার জন্য একটি built-in কম্পোজেবল (Composable)। এটি server-side rendering (SSR) এবং client-side rendering (CSR) উভয় ক্ষেত্রে কাজ করে।

```
const { data: posts, pending, error } = useFetch('https://jsonplaceholder.typicode.com/posts');
```

🔍 কোড ব্যাখ্যা:
✅ useFetch() API থেকে ডাটা ফেচ করে।
✅ pending লোডিং অবস্থা চেক করে।
✅ error থাকলে তা দেখায়।
✅ data: posts দ্বারা ফেচ করা ডাটা reactive হয়।

##### Set baseURL from Nuxt Config and use useFetch().

In **Nuxt.js**, you can set a global API `baseURL` in the `nuxt.config.ts` file.

📌 **Step 1:** Set `baseURL` in `nuxt.config.ts`

```
export default defineNuxtConfig({
  runtimeConfig: {
    public: {
      apiBase: 'https://jsonplaceholder.typicode.com'
    }
  }
});

```
*Now, when we use useFetch(), the baseURL will be automatically included.

##### Using `baseURL` with `useFetch()`.

```

const config = useRuntimeConfig();
const { data: posts, pending, error } = useFetch(`${config.public.apiBase}/posts`);

```

🔍 **Code Explanation:**

✅ Using `useRuntimeConfig()`, I retrieved `apiBase` from `nuxt.config.ts`.

✅ In `useFetch()`, I used `${config.public.apiBase}/posts` to make the API call.

##### Query Parameter Usage (Dynamic Fetching)

Sometimes, we need to send dynamic data. You can send query parameters with `useFetch()`.

📌 **Example 3:** Using Dynamic Query Parameters with `useFetch()`

```
const userId = ref(1);
const { data: posts, pending, error } = useFetch('https://jsonplaceholder.typicode.com/posts', {
  query: { userId }
});
```

## useAsyncData 

##### useAsyncData() হল Nuxt.js এর একটি Composable ফাংশন, যা server-side rendering (SSR) এবং client-side rendering (CSR) উভয় ক্ষেত্রে ডাটা ফেচিং করার জন্য ব্যবহৃত হয়। এটি API কল বা ডাটা ফেচিং করার জন্য একটি শক্তিশালী এবং পারফরম্যান্স-ভিত্তিক সমাধান।

```
const { data, pending, error } = await useAsyncData('userProfile', () => 
  $fetch('https://jsonplaceholder.typicode.com/users/1')
);
```

🔍 **Code Explanation:**

✅ Here, data is fetched for the user using the key `userProfile`.

✅ The data is displayed using `data.name` and `data.email`.


## Asxios with useAsnycData

##### To add Axios configuration in nuxt.config.js, you can follow these steps:

##### Configure Axios in nuxt.config.js:

```
// nuxt.config.js
export default {
  modules: ['@nuxtjs/axios'],
  axios: {
    baseURL: 'https://jsonplaceholder.typicode.com', // API এর base URL দিন
  },
}
```

##### Creating an Async Function with Axios: Get Method

*Now you can fetch data using useAsyncData and Axios.

```
const { data, pending, error } = await useAsyncData('posts', async () => {
  try {
    const response = await $axios.get('/posts');
    return response.data;
  } catch (err) {
    console.error(err);
    throw new Error('Failed to fetch posts');
  }
});
```

#### Code Explanation:

- **useAsyncData:** This is used to fetch data. Here, `'posts'` is the key, which is used for caching.
- **$axios.get('/posts'):** This fetches data from the `/posts` endpoint via Axios.
- **pending:** This is used to check the loading state. "Loading..." is displayed while the data is being loaded.
- **error:** If any error occurs, it is shown here.
- **data:** This is where the fetched data is stored.


##### Creating an Async Function with Axios: Post Method

```


import { ref } from 'vue';

const newPost = ref({
  title: 'New Post',
  body: 'This is a new post created via axios in Nuxt.js.',
  userId: 1
});

const createPost = async () => {
  try {
    const response = await $axios.post('/posts', newPost.value);
    console.log('Post Created:', response.data);
  } catch (error) {
    console.error('Error creating post:', error);
  }
};




```


#### orgApiClient Data

## for necessary 

```
const { $orgUserApiClient } = useNuxtApp();
const authStore = useOrgAuthStore();
const organizationId = authStore.orgUser?.organizationId ?? 1;

// Filter and pagination for fetching data
const filter = ref<PaginationFilter>({
  sort: 'name',
  fields: 'id, name',
  perPage: 5,
  page: 1,
  // filter: { name: '' },
});

```
### main api integreat function
```
 try {
    const orgJourneyApiResponse = await $orgUserApiClient
      ?.journeyApi
      ?.getAll(organizationId, filter.value);
    journeyList.value = orgJourneyApiResponse.data;
    // pageMeta.value = orgJourneyApiResponse.meta;
  } catch (error) {
    throw error;
  }
  
  ```




======= Error Handeling =======

## Step -1 : To create a error.vue page in the root directory.

```
<p>{{ error.statusCode }}</p>
<p>{{error.message}}</p>
<button class="btn my-7" @click="handelClearError"> clear error.</button>
<script setup>

defineProps(['error'])
const handelClearError = () => clearError({ redirect: '/' })

</script>
```

###### and we can see error when we can't find our products/elements. then we can use belows code which is connected in error.vue page. these codes are set in the <script/> tag.

```
if(!product.value){
  // const error = new Error('Product not found')
  // error.statusCode = 404
  // throw error
throw createError({statusCode:404,message:'Product not found'})

}
```


### useAsyncData Error Handeling

```
<script setup>
const { data, error } = useAsyncData('userProfile', async () => {
  const response = await $fetch(`${config.public.apiBase}/user`);
  return response;
});

if (error.value) {
  console.error('Error fetching data:', error.value);
}
</script>
```

#### Task-1 
  * দুইটা বাটন রাখবেন (increase,decrease) এন্ড একটি কাউন্টার সেট করবেন. বাটন দুইটাতে ক্লিক করলে কাউন্টারের  মান বাড়বে কমবে


 ![Alt Text](https://i.ibb.co.com/Hp7MLWHT/Capture4.png)


  ![Alt Text](https://i.ibb.co.com/xSkthzJY/Capture5.png)


  * দুইটা বাটন রাখবেন (increase,decrease) এন্ড একটি কাউন্টার সেট করবেন.বাটন দুইটাতে ক্লিক করলে কাউন্টারের  মান বাড়বে কমবে একই সাথে দুইটা কম্পোনেন্ট থেকে।
  
  * উপরিউক্ত কাজ গুলো কম্পোজাবল ব্যবহার করে করবেন.এন্ড পারসিস্ট করে rekhe dite hobe. যেন রিলোড দিলেও ডাটা না চলে যায়।।.

  ![Alt Text](https://i.ibb.co.com/DZ3Cqz3/Capture9.png)
  

  
#### Task-2
একটা টাস্ক্লিস্ট থেকে একজন ইউজার একাদিক টাস্ক সিলেক্ট করতে পারবে এন্ড সিলেক্টেড টাস্ক ড্রাগবল করা যাবে,এডিট করা যাবে,ডিলিট করা যাবে - এক্কেত্রে পিনিয়া স্টোর ব্যবহার করে করবেন। পারসিস্ট করে rekhe dite hobe. যেন রিলোড দিলেও ডাটা না চলে যায়।।.

![Alt Text](https://i.ibb.co.com/p6J0Qrcw/Capture1.png)

### Task-3
 ৩ টি ক্যাটাগরি থাকবে---
- Category 1  এ থাকবে নিচের টাস্ক গুলো 
    - C1 Task1
    - C1 Task2
    - C1 Task3
- Category 2 এ থাকবে নিচের টাস্ক গুলো 
    - C2 Task1
    - C2 Task2
- Category 3 টাস্ক থাকবে না।


ক্যাটাগরি লিস্ট থেকে একটা ক্যাটাগরি সিলেক্ট করলে তার নিচে সিলেক্টেড টাস্ক দেখাবে, সিলেক্টেড টাস্ক সিলেক্ট করে সাবমিসন বাটনে ক্লিক করলে সাবমিট হবে, পারসিস্ট করে rekhe dite hobe. যেন রিলোড দিলেও ডাটা না চলে যায়।।.


![Alt Text](https://i.ibb.co.com/bRXP326t/Capture.png)


jokhon - টাস্ক সিলেক্ট করা হবে তখন সাবমিট বাটন দেখাবে এন্ড সাবমিট করা হলে টেম্পলেট গুলো ফাকা হয় পুনরায় আগের অবস্তানে ফেরত আসবে। এবং একটী alert dekhabe. বাটন দেখাবে না, সাবমিসনের পরে।

![Alt Text](https://i.ibb.co.com/m5HyPvFN/Captureqq.png)



![Alt Text](https://i.ibb.co.com/TyCHzYn/jjj.png)