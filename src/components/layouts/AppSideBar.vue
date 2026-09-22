<script setup>
    import { RouterLink } from 'vue-router';
</script>

<template>
    <nav>
        <div class="flex-column nav-pills gap-1">
            <Routerlink to="/pets">
                <i class="fa-regular fa-star"></i>
                pets
            </Routerlink>
            <Routerlink to="/pets/novo">
                <i class="fa-regular fa-plus-circle"></i>
                
            </Routerlink>
        </div>
    </nav>

</template>