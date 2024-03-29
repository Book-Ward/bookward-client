<template>
    <div @click="openUserProfile(userId)" class="bg-slate-400 p-8 m-4 rounded cursor-pointer">
        <div class="flex items-center">
            <div>
                <div class="text-lg font-medium text-white">
                    {{ username }}
                </div>
                <div class="text-sm text-gray-300">
                    {{ userId }}
                </div>
                <div v-if="!hideFollowing" @click.stop>
                    <Button @click="followUser" :disabled="supaUserId === userId" :class="supaUserId === userId ? 'cursor-not-allowed' : ''" class="mt-4 w-full">
                        {{ isFollowingFlag ? "Following" : "Follow" }}
                    </Button>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { useToast } from 'vue-toastification'

const toast = useToast()

const { username, userId, isFollowing, hideFollowing } = defineProps(['username', 'userId', 'isFollowing', 'hideFollowing'])
const isFollowingFlag = ref(isFollowing)

const user = useSupabaseUser()
const supaUserId = computed(() => user.value?.id)

const openUserProfile = (userId) => {
    navigateTo(`/profile/${userId}`)
}

const followUser = async () => {
    if (!user.value) {
        navigateTo('/login')
        return
    }

    if (supaUserId.value === userId) {
        return
    }

    isFollowingFlag.value = !isFollowingFlag.value

    await $fetch(useRuntimeConfig().apiBase + `/users/followed`, {
        method: 'post',
        headers: { 'Authorization': `Bearer ${useSupabaseToken().value}` },
        body: {
	        userToFollowId: userId
        }
    }).then ((res) => {        
        if (isFollowingFlag.value)
        {
            toast.success(`Followed ${username} successfully 🎉`)
            return;
        }

        toast.success(`Unfollowed ${username} successfully 😢`)
    }).catch ((err) => {
        console.error(err)

        toast.error(`Failed to follow/unfollow ${username} 😢`)

        isFollowingFlag.value = !isFollowingFlag.value
    })
}

</script>
