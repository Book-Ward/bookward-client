<template>
    <div class="xl:w-64 w-max m-3">
        <form @submit.prevent="searchByName" class="">
            <input
            v-model="query"
            type="search"
            class="form-control block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none"
            placeholder="Find your next book"
            autocomplete="off"
            />
        </form>
    </div>
</template>

<script setup>
const apiBase = useRuntimeConfig().apiBase

const title = useState('title', () => '');
const books = useState('books', () => []);
const genres = useState('genres', () => []);
const fromYear = useState('fromYear', () => '');
const toYear = useState('toYear', () => '');
const rating = useState('rating', () => 3);

const query = ref(title.value)

// Search book by name
const searchByName = async () => {
    title.value = query.value

    // If the query is not empty, send the request
    if (query.value.length > 0 || 
        genres.value.length > 0 ||
        fromYear.value != '' ||
        toYear.value != '' ||
        rating.value > 3)
    {
        await $fetch(apiBase + `/book/search/`, { 
            method: 'post',
            headers: { 'Authorization': `Bearer ${useSupabaseToken().value}` },
            body: {
                title: title.value,
                genres: genres.value,
                from: fromYear.value,
                to: toYear.value,
                rating: rating.value
            }
        }).then( (res) => {
            books.value = res
        })

        return navigateTo({
            path: '/search',
        })
    // else return to the explore page
    } else {
        return navigateTo({
            path: '/explore',
        })
    }
}

watch(genres.value, () => {
    searchByName()
})

watch(fromYear, () => {
    searchByName()
})

watch(toYear, () => {
    searchByName()
})

watch(rating, () => {
    searchByName()
})

</script>