<template>
    <main>
        <div>
            <table>
                <caption>Les produits - page {{ data.page.number + 1 }} / {{ data.page.totalPages }} </caption>
                <tr>
                    <th>Nom</th>
                    <th>Prix</th>
                    <th>Stock</th>
                    <th>En commande</th>
                </tr>
                <!-- Si le tableau est vide -->
                <tr v-if="data.listeProduits.length === 0">
                    <td colspan="4">Veuillez patienter, chargement des produits...</td>
                </tr>
                <!-- Si le tableau n'est pas vide -->
                <tr v-for="produit in data.listeProduits" :key="produit">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                </tr>
            </table>
            <div class="pagination">
                <button @click="chargeProduits(0)">
                    Début
                </button>
                <button @click="data.page.number + 1 > 1 ? chargeProduits(data.page.number - 1) : ''">
                    Précédent
                </button>
                <button @click="data.page.number + 1 < data.page.totalPages ? chargeProduits(data.page.number + 1) : ''">
                    Suivant
                </button>
                <button @click="chargeProduits(data.page.totalPages - 1)">
                    Fin
                </button>
            </div>
        </div>
    </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { doAjaxRequest, APIError } from "../api";

let data = reactive({
    // La liste des produits affichée sous forme de table
    listeProduits: [],
    // Informations de la page
    page: {}
});

function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
}

/**
 * Appel à l'API pour avoir la liste des produits
 * Enregistre les produits dans un tableau d'une page passée en paramètre
 * Trié par nom, ascendant
 * @param {number} nPage Numéro de la page
 */
function chargeProduits(nPage) {
    doAjaxRequest(`/api/produits?page=${nPage}&size=5&sort=nom,asc`)
        .then((json) => {
            data.listeProduits = json._embedded.produits;
            data.page = json.page;
        })
        .catch(showError);
}

// A l'affichage du composant, on affiche la liste des produits de la 1ère page
onMounted(() => {
    chargeProduits(0);
});
</script>


<style scoped>
body {
  font-family: 'Courier New', Courier, monospace;
}

table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #f2f2f2;
}

th {
  background-color: #f7971d;
  color: black;
  font-weight : 900
}

button {
  background-color: #000000;
  color: white;
  border: none;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-weight: 900;
}
</style>