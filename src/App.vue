<template>
    <v-app :theme="theme">
        <v-navigation-drawer location="start" permanent>
            <template #prepend>
                <div class="pa-7">
                    <v-img :src="logo" alt="" />
                </div>
                <v-divider />
            </template>
            <template #append>
                <v-footer>
                    <small>&copy; 2024 <em>Your name here</em></small>
                </v-footer>
            </template>
            <v-list open-strategy="single">
                <template v-for="route in routes" :key="route.name">
                    <v-list-group v-if="route.children.length > 0">
                        <template #activator="{ props }">
                            <v-list-item
                                v-bind="props"
                                color="primary"
                                :prepend-icon="route.meta.menuItem?.icon"
                                :title="route.meta.menuItem?.title"
                            />
                        </template>
                        <v-list-item
                            v-for="child in route.children"
                            :key="child.name"
                            color="primary"
                            :prepend-icon="child.meta?.menuItem?.icon"
                            :title="child.meta?.menuItem?.title"
                            :to="child"
                            :value="child.name"
                        />
                    </v-list-group>
                    <v-list-item
                        v-else
                        color="primary"
                        :prepend-icon="route.meta.menuItem?.icon"
                        :title="route.meta.menuItem?.title"
                        :to="route"
                        :value="route.name"
                    />
                </template>
            </v-list>
        </v-navigation-drawer>
        <v-app-bar color="primary">
            <template #title>
                <v-app-bar-title class="text-h5"><em>Application Name</em></v-app-bar-title>
            </template>
            <template #append>
                <v-menu>
                    <template #activator="{ props }">
                        <v-btn
                            v-bind="props"
                            prepend-icon="mdi-account"
                            rounded="pill"
                            size="x-large"
                            variant="tonal"
                            :text="name"
                        />
                    </template>
                    <v-list>
                        <v-list-item
                            lines="two"
                            prepend-icon="mdi-account"
                            subtitle="Signed In"
                            :title="name"
                        />
                        <v-list-item
                            lines="two"
                            prepend-icon="mdi-account-settings-outline"
                            subtitle="Adjust Preferences"
                            title="Settings"
                        />
                        <v-divider />
                        <v-list-item prepend-icon="mdi-logout" title="Sign Out" @click="signOut" />
                    </v-list>
                </v-menu>
            </template>
        </v-app-bar>
        <v-main class="d-flex flex-column flex-1-1-100">
            <router-view v-slot="{ Component, route }" name="main">
                <component :is="Component" :key="route.name" />
            </router-view>
        </v-main>
    </v-app>
</template>

<script setup lang="ts">
import { computed, provide, unref } from "vue";
import { RouterView } from "vue-router";

import logo from "./logo.png";
import { byIndex, hasMenuItem } from "./routes";
import type { Services } from "./Services";
import { defineStore, StoreKey } from "./Store";
import useTheme from "./Theme";

const props = defineProps<{
    services: Services;
}>();

const stores = defineStore(props.services);
provide(StoreKey, stores);

const { router } = stores;
const { topLevelRoutes } = router;

const theme = useTheme();
const routes = computed(() =>
    unref(topLevelRoutes)
        .filter((route) => hasMenuItem(route))
        .sort(byIndex),
);
const name = computed<string>(() => "User Name");
function signOut() {}
</script>

<style lang="scss">
@use "@fontsource/roboto/latin-400";

body {
    font-family: "Roboto", sans-serif;
}

a {
    text-decoration: none;
}

a[target="_blank"]::after {
    content: " \0f0327";
    font-family: "Material Design Icons", fantasy;
    font-variant-position: super;
    text-decoration: none;
}
</style>
