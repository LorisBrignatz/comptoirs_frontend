<template>
    <main>
        <div>
            <!-- Un formulaire pour saisir les valeurs du produit à ajouter -->
            <form @submit.prevent="ajouteProduit">
                <div>
                    <input id="libelle" v-model="data.formulaireProduit.libelle" placeholder="Libelle" />
                </div>
                <div>
                    <input id="description" v-model="data.formulaireProduit.description" placeholder="Description" />
                </div>
                <button type="submit">Ajouter</button>
            </form>
        </div>
        <div>
            <table>
                <caption>Liste des produits</caption>
                <tr>
                    <th>Nom</th>
                    <th>Prix</th>
                    <th>Stock</th>
                    <th>Commandés</th>
                </tr>
                <!-- Si le tableau des produits est vide -->
                <tr v-if="!data.listeProduits">
                    <td colspan="4">Veuillez patienter, chargement des produits...</td>
                </tr>
                <!-- Si le tableau des produits n'est pas vide -->
                <tr v-for="produit in data.listeProduits" :key="produit.code">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                    <td>
                        <button @click="deleteEntity(produit._links.self.href)">
                            Supprimer
                        </button>
                    </td>
                </tr>
            </table>
        </div>
    </main>
</template>

<script setup>
import {BACKEND, doAjaxRequest} from "../api";
import {onMounted, reactive} from "vue";

// Pour réinitialiser le formuaire
const produitVide = {
    reference: "",
    nom: ""
};

let data = reactive({
    // Les données saisies dans le formulaire
    formulaireProduit: { ...produitVide },
    // La liste des produits affichée sous forme de table
    listeProduits: []
});

function chargeCategories() {
    // Appel à l'API pour avoir la liste des produits
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    doAjaxRequest("https://springajax.herokuapp.com/api/produits?page=" + data.page + "&size=" + data.size)
        .then((json) => {
            data.listeProduits = json._embedded.produits;
            data.totalPages = json.page.totalPages-1;
        })
        .catch((error) => alert(error.message));
}

function ajouteProduit() {
    // Ajouter un produit avec les données du formulaire
    const options = {
        method: "POST", // Verbe HTTP POST pour ajouter un enregistrement
        body: JSON.stringify(data.formulaireProduit),
        headers: {
            "Content-Type": "application/json",
        },
    };
    doAjaxRequest(BACKEND + "/api/produits", options)
        .then(() => {
            // Réinitialiser le formulaire
            data.formulaireProduit = { ...produitVide };
            // Recharger la liste des produits
            chargeProduits();
        })
        .catch((error) => alert(error.message));
}

/**
 * Supprime une entité
 * @param entityRef l'URI de l'entité à supprimer
 */
function deleteEntity(entityRef) {
    doAjaxRequest(entityRef, { method: "DELETE" })
        .then(chargeProduits)
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