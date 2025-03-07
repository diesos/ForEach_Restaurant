<!-- eslint-disable vue/multi-word-component-names -->
<script setup>
import { ref, computed } from "vue";
import { reservationsApi } from "@/api/requests";
import { useToast } from "vue-toastification";
import { errorHandler } from "@/utils/errorHandler";
import { defineEmits } from 'vue';

// Définition des événements émis par le composant
const emit = defineEmits(['reservation-created']);

const toast = useToast();

// Variables réactives pour les champs du formulaire
const selectedDate = ref("");
const selectedTime = ref("");
const nbPeople = ref(1);

// Calcul dynamique de la date minimale (aujourd'hui)
const today = new Date();
const minDate = computed(() => {
    return today.toISOString().split('T')[0]; // Formate la date en YYYY-MM-DD
});

// Créneaux horaires disponibles : matin et soir, par pas de 30 minutes
const morningSlots = ["12:00", "12:30", "13:00", "13:30", "14:00"];
const eveningSlots = ["20:00", "20:30", "21:00", "21:30", "22:00"];
const allSlots = computed(() => [...morningSlots, ...eveningSlots]);

// Fonction de soumission du formulaire
const submitForm = async () => {
    if (!selectedDate.value || !selectedTime.value) {
        alert("Veuillez sélectionner une date et une heure.");
        return;
    }

    try {
        // Création de la date et de l'heure complètes
        const datetimeString = `${selectedDate.value}T${selectedTime.value}:00`;
        const dateDebut = new Date(datetimeString);

        // Durée fixe de l'événement : 1 heure
        const maxDuration = 60 * 60 * 1000; // 1 heure en millisecondes
        const dateFin = new Date(dateDebut.getTime() + maxDuration);

        // Envoi des données à l'API
        await reservationsApi.createReservation({
            dateDebut: dateDebut.toISOString(),
            dateFin: dateFin.toISOString(),
            nbPersonnes: nbPeople.value
        });

        // Affichage d'une notification de succès
        toast.success("Réservation ajoutée!");

        // Réinitialisation des champs du formulaire
        selectedDate.value = "";
        selectedTime.value = "";
        nbPeople.value = 1;

        // Émission de l'événement pour notifier la création d'une réservation
        emit('reservation-created');
    } catch (error) {
        errorHandler(error, "Erreur lors de la création du rendez-vous");
    }
};
</script>

<template>
<div class="pb-4 rounded mx-auto">
    <h3 class="text-3xl font-bold">Réservez</h3>
    <hr class="mb-5 h-0.5 border-t-0 bg-cyan-700" />

    <form @submit.prevent="submitForm">
        <div class="flex flex-wrap gap-4">

            <!-- Sélection de la date -->
            <div class="flex-1">
                <label for="date" class="block text-sm font-medium text-gray-700">Date</label>
                <input
                    type="date"
                    id="date"
                    v-model="selectedDate"
                    :min="minDate"
                    class="mt-1 block w-full border border-gray-300 rounded-md p-2"
                    required
                />
            </div>

            <!-- Sélection de l'heure -->
            <div class="flex-1">
                <label for="time" class="block text-sm font-medium text-gray-700">Heure</label>
                <select
                    id="time"
                    v-model="selectedTime"
                    class="mt-1 block w-full border border-gray-300 rounded-md p-2"
                    required
                >
                    <option disabled value="">Veuillez sélectionner une heure</option>
                    <option v-for="time in allSlots" :key="time" :value="time">
                        {{ time }}
                    </option>
                </select>
            </div>

            <!-- Nb de personnes -->
            <div class="flex-2">
                <label for="nbPeople" class="block text-sm font-medium text-gray-700">Nombre de personnes</label>
                <input
                    type="number"
                    id="nbPeople"
                    v-model.number="nbPeople"
                    min="1"
                    max="10"
                    class="mt-1 block w-full border border-gray-300 rounded-md p-2"
                    required
                />
            </div>

            <!-- Bouton submit -->
            <button
                type="submit"
                class="flex-2 bg-cyan-700 text-white py-2 px-4 mt-6 rounded hover:bg-cyan-600"
            >
                Réserver
            </button>
        </div>
    </form>
</div>
</template>
