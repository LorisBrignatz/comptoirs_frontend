<template>
    <main>
        <div>
            <table>
                <caption>Liste des produits</caption>
                <tr>
                    <th>Nom</th>
                    <th>Prix</th>
                    <th>Stock</th>
                    <th>Commandés</th>
                </tr>
                <!-- Si le tableau des produits n'est pas vide -->
                <tr v-for="produit in data.listeProduits" :key="produit.reference">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                </tr>
            </table>
        </div>
    </main>
</template>

<script setup>
import {onMounted, reactive} from "vue";
import {BACKEND, doAjaxRequest} from "../api";

let data = reactive({
    // La liste des produits affichée sous forme de table
    listeProduits: []
});

function chargeProduits() {
    // Appel à l'API pour avoir la liste des produits
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    doAjaxRequest("https://springajax.herokuapp.com/api/produits?page=0&size=5")
        .then((json) => {
            data.listeProduits = json._embedded.produits;
        })
        .catch((error) => alert(error.message));
}

// A l'affichage du composant, on affiche la liste
onMounted(chargeProduits);

</script>


<style scoped>
td,
th {
    border: 1px solid #ddd;
    padding: 8px;
}


th {
    padding-top: 12px;
    padding-bottom: 12px;
    text-align: left;
    background-color: #232623;
    color: rgb(255, 255, 255);
}
</style>