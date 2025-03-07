<!-- eslint-disable vue/multi-word-component-names -->

<script setup>
import { ref, computed } from 'vue';

const isHovered = ref(false);
const userString = localStorage.getItem("user");

// Vérification du rôle de l'utilisateur de manière réactive
const isAdmin = computed(() => {
    if (!userString) return false;
    try {
        const user = JSON.parse(userString);
        return user.role === 'admin';
    } catch (error) {
        console.error("Erreur lors de l'analyse des données utilisateur", error);
        return false;
    }
});
</script>

<template>
<nav
    class="menubar"
    :style="{ width: isHovered ? '200px' : '65px' }"
    @mouseenter="isHovered = true"
    @mouseleave="isHovered = false"
>
    <ul>
        <li>
            <router-link to="user">
                <font-awesome-icon :icon="['fas', 'calendar-plus']" />
                <span class="textMenu">Réservations</span>
            </router-link>
        </li>
        <li v-if="isAdmin">
            <router-link to="admin">
                <font-awesome-icon :icon="['fas', 'user-shield']" />
                <span class="textMenu">Espace Admin</span>
            </router-link>
        </li>
        <li>
            <router-link to="/logout">
                <font-awesome-icon :icon="['fas', 'sign-out-alt']" />
                <span class="textMenu">Déconnexion</span>
            </router-link>
        </li>
    </ul>
</nav>
</template>

<style scoped>
.menubar {
    background-color: #1a1a1a;
    color: #ffffff;
    height: 100vh;
    overflow: hidden;
    padding: 20px;
    box-shadow: 3px 0 6px rgba(0, 0, 0, 0.3);
    position: fixed;
    top: 0;
    left: 0;
    transition: width 0.3s ease-in-out;
}

.menubar ul {
    list-style: none;
    padding: 0;
    margin: 0;
}

.menubar ul li {
    display: flex;
    align-items: center;
    margin: 15px 0;
    padding: 10px 0;
}

.menubar ul li a {
    display: flex;
    align-items: center;
    text-decoration: none;
    color: #ffffff;
    font-size: 14px;
    font-weight: 600;
    transition: color 0.3s;
}

.menubar ul li a:hover {
    color: #4fa3ff;
}

.textMenu {
    opacity: 0;
    transition: opacity 0.3s ease-in-out;
    white-space: nowrap;
    padding-left: 1rem;
}

.menubar:hover .textMenu {
    opacity: 1;
}
</style>
