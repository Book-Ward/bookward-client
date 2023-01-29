<template>
    <div class="bg-slate-400 p-8 m-4 rounded">
        <div class="flex items-center">
            <div>
                <div class="text-lg font-medium text-white">
                    {{ username }}
                </div>
                <div class="text-sm text-gray-300">
                    {{ userId }}
                </div>
                <div>
                    <Button @click="followUser" :disabled="supaUserId === userId" :class="supaUserId === userId ? 'cursor-not-allowed' : ''" class="mt-4 w-full">Follow</Button>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
const { username, userId } = defineProps(['username', 'userId'])

const user = useSupabaseUser()
const supaUserId = computed(() => user.value?.id)


const followUser = async () => {
    if (!supaUserId.value) {
        navigateTo('/login')
        return
    }

    if (supaUserId.value === userId) {
        return
    }

    await $fetch(useRuntimeConfig().apiBase + `/followUser`, {
        method: 'post',
        body: {
            userId: supaUserId.value,
	        userToFollowId: userId
        }
    }).then ((res) => {
        console.log(res)
    }).catch ((err) => {
        console.error(err)
    })
}

</script>