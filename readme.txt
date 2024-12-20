Chatty Bot v2
--------------

Description :
--------------
Chatty Bot v2 est un chatbot interactif sous GNU GENERAL PUBLIC LICENSE Version 3, développé en HTML, CSS, JavaScript et PHP.
Il permet de répondre à des questions en utilisant des données d'entraînement stockées localement en dur JSON et dans un fichier ingrémentiel JSON. 
Il inclut également un mécanisme de formation dynamique pour ajouter de nouvelles données d'entraînement à la volée. Le projet supporte des thèmes clairs et sombres, facilement commutables via un bouton.

Fonctionnalités :
------------------
 - Réponses Automatiques : Répond aux questions en utilisant des données d'entraînement.

 - Formation Dynamique : Permet d'ajouter de nouvelles questions et réponses à la base de données.

 - Commande Vocale : Lit les réponses à haute voix.

 - Commandes Spéciales : Supporte des commandes spécifiques comme afficher l'heure ou ouvrir des sites web.

 - Thèmes Clairs et Sombres : Commutation entre les thèmes via un bouton.

Prérequis :
------------
 - Serveur web (par ex. Apache, nginx) avec support PHP.

 - Navigateur moderne avec support pour JavaScript.

Installation :
---------------
 - Clonez le repository ou téléchargez les fichiers.

 - Placez les fichiers dans le répertoire du serveur web.

 - Assurez-vous que le serveur web peut accéder et exécuter des scripts PHP.

Fichiers :
-----------
index.html : Le fichier principal HTML contenant le chatbot et les scripts nécessaires.
light-theme.css : Feuille de style pour le thème clair.
dark-theme.css : Feuille de style pour le thème sombre.
browser.js : Contient le code JavaScript principal pour la fonctionnalité du chatbot. (brain js pour browser)
save_training_data.php : Script PHP pour sauvegarder les données d'entraînement.
trainingData.json : Fichier JSON pour stocker les données d'entraînement.
responses.json : Fichier JSON contenant les réponses prédéfinies du chatbot.

Structure du Projet :
----------------------
/chatty-bot-v2
├── index.html
├── light-theme.css
├── dark-theme.css
├── browser.js
├── save_training_data.php
├── trainingData.json
└── responses.json

Utilisation :
--------------
1) Commutation entre les Thèmes

Le chatbot supporte les thèmes clairs et sombres, commutables via un bouton.
Envoi de Messages et Formation Dynamique

2) Envoi de Messages et Formation Dynamique

Les utilisateurs peuvent envoyer des messages et recevoir des réponses en utilisant l'interface du chatbot. Si aucune réponse n'est trouvée, ils peuvent ajouter de nouvelles données d'entraînement :

submitButton.addEventListener('click', () => {
    const userInput = inputField.value.trim().toLowerCase();
    let response = findResponse(userInput, jsonData, trainingData);
    if (!response) {
        response = null; // Aucune réponse trouvée dans les données d'entraînement ou JSON
        trainingContainer.style.display = 'flex';
        document.getElementById('trainingQuestion').value = userInput;
        inputField.value = ''; // Effacer le contenu de l'input après soumission
        return;
    }
    // Ajout de la réponse au conteneur des réponses
});

3) Commandes Spéciales

Le chatbot supporte des commandes spécifiques telles que heure, date, clear, stop, et ouvrir des sites web comme Google, Bing, etc.

const specialKeywords = {
    "heure": displayCurrentTime,
    "date": displayCurrentDate,
    "clear": clearMessages,
    "stop": stopSpeech,
    "google": () => window.open("https://www.google.com", "_blank"),
    "bing": () => window.open("https://www.bing.com", "_blank"),
    "wikipedia": () => window.open("https://fr.wikipedia.org/wiki/Wikipédia", "_blank"),
    "projets raspberry": () => window.open("https://projects.raspberrypi.org/fr-FR", "_blank"),
    "kubii": () => window.open("https://www.kubii.com/fr/", "_blank"),
    "boot": () => window.open("http://127.0.0.1:8000/system-UIX/boot.html", "_blank"),
    "meteo": () => window.open("https://wttr.in/digoin", "_blank"),
    "metar": () => window.open("https://www.meteoblue.com/fr/meteo/cartes/widget/paris-12_france_6618618", "_blank")
};

4) Sauvegarde et Chargement des Données d'Entraînement

Le script PHP save_training_data.php permet de sauvegarder les nouvelles données d'entraînement :

<?php
header('Content-Type: application/json');

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $input = file_get_contents('php://input');
    $data = json_decode($input, true);

    if (json_last_error() === JSON_ERROR_NONE) {
        if (file_put_contents('trainingData.json', json_encode($data, JSON_PRETTY_PRINT))) {
            echo json_encode(['status' => 'success']);
        } else {
            http_response_code(500);
            echo json_encode(['status' => 'error', 'message' => 'Erreur lors de la sauvegarde des données']);
        }
    } else {
        http_response_code(400);
        echo json_encode(['status' => 'error', 'message' => 'Invalid JSON']);
    }
} else {
    http_response_code(405);
    echo json_encode(['status' => 'error', 'message' => 'Method Not Allowed']);
}
?>

5) Configuration du Réseau de Neurones

Le chatbot utilise la bibliothèque de brain.js issu initialement pour nodejs et ici on utilise uniquement browser.js la version pour web browser et code natif, pour entraîner un réseau de neurones. Voici les détails de la configuration et des options utilisées :

Initialisation du Réseau de Neurones
-------------------------------------

Le réseau de neurones est initialisé avec les paramètres suivants :

const net = new brain.NeuralNetwork({
    hiddenLayers: [3, 3], // Couches cachées : 3 couches de 3 neurones chacune
    learningRate: 0.6, // Taux d'apprentissage
    activation: 'sigmoid', // Fonction d'activation (peut être 'sigmoid', 'relu', 'leaky-relu', 'tanh')
    errorThresh: 0.00001, // Seuil d'erreur
    log: true, // Activer les logs pour suivre l'entraînement
    logPeriod: 100, // Périodicité des logs
    momentum: 0.5, // Taux de momentum pour l'entraînement
    binaryThresh: 0.5 // Seuil pour les valeurs binaires
});

Options de Configuration

 - hiddenLayers : Spécifie le nombre et la taille des couches cachées. Ici, il y a 3 couches de 3 neurones chacune.

 - learningRate : Le taux d'apprentissage du réseau, ici fixé à 0.6.

 - activation : La fonction d'activation utilisée par les neurones (options possibles : sigmoid, relu, leaky-relu, tanh). Ici, sigmoid est utilisée.

 - errorThresh : Le seuil d'erreur en dessous duquel l'entraînement s'arrête, ici fixé à 0.00001.

 - log : Active les logs pour suivre l'entraînement.

 - logPeriod : La périodicité des logs, ici tous les 100 itérations.

 - momentum : Le taux de momentum pour l'entraînement, ici fixé à 0.5.

 - binaryThresh : Le seuil pour les valeurs binaires, ici fixé à 0.5.

Entraînement du Réseau de Neurones

 - La fonction suivante est utilisée pour entraîner le réseau de neurones avec les données d'entraînement fournies :

function trainNeuralNetwork(net, trainingData) {
    try {
        if (trainingData.length > 0) {
            console.log('Training Data (Avant formatage):', trainingData);

            // Correction: Vérification et normalisation des données
            const formattedData = trainingData.map(item => ({
                input: typeof item.input === 'string' ? item.input : JSON.stringify(item.input),
                output: {
                    response: typeof item.output.response === 'string' ? item.output.response : JSON.stringify(item.output.response)
                }
            }));

            console.log('Formatted Data (Après formatage):', formattedData);
            net.train(formattedData, {
                iterations: 10000,
                log: true,
                learningRate: 0.6,
                errorThresh: 0.00001,
                callback: function (status) { // Fonction de rappel pour chaque période de log
                    console.log(`Itérations : ${status.iterations}, Erreur d'entraînement : ${status.error}`); // Ajout du log ici
                },
                callbackPeriod: 100 // Périodicité de la fonction de rappel
            });
            console.log('Réentraînement terminé');
        } else {
            console.log('Aucune donnée d\'entraînement disponible');
        }
    } catch (error) {
        console.error('Erreur lors de l\'entraînement du réseau de neurones:', error);
    }
}

if (trainingData.length > 0) {
    console.log('Entraînement initial du réseau de neurones...');
    trainNeuralNetwork(net, trainingData);
}

Cette fonction vérifie si des données d'entraînement sont disponibles, les formate si nécessaire, puis entraîne le réseau de neurones avec ces données.

Exécution

 - Le réseau de neurones est d'abord entraîné avec les données d'entraînement disponibles lors du chargement de la page, comme montré ci-dessus.

6) Fonction de Calcul de la Distance de Levenshtein

Le script utilise la fonction de calcul de la distance de Levenshtein pour mesurer la similarité entre deux chaînes de caractères. Cela permet au chatbot de trouver des réponses même si l'entrée utilisateur ne correspond pas exactement aux mots-clés définis.
Distance de Levenshtein.

La distance de Levenshtein, également connue sous le nom de distance d'édition, est une mesure du nombre minimal d'opérations nécessaires pour transformer une chaîne de caractères en une autre. Les opérations permises sont l'insertion, la suppression et la substitution de caractères.

Implémentation de la Fonction :

function levenshtein(a, b) {
    const matrix = [];

    if (a.length === 0) return b.length;
    if (b.length === 0) return a.length;

    for (let i = 0; i <= b.length; i++) {
        matrix[i] = [i];
    }

    for (let j = 0; j <= a.length; j++) {
        matrix[0][j] = j;
    }

    for (let i = 1; i <= b.length; i++) {
        for (let j = 1; j <= a.length; j++) {
            if (b.charAt(i - 1) === a.charAt(j - 1)) {
                matrix[i][j] = matrix[i - 1][j - 1];
            } else {
                matrix[i][j] = Math.min(
                    matrix[i - 1][j - 1] + 1, // Substitution
                    Math.min(
                        matrix[i][j - 1] + 1, // Insertion
                        matrix[i - 1][j] + 1 // Suppression
                    )
                );
            }
        }
    }

    return matrix[b.length][a.length];
}

Utilisation de la Fonction dans le Script

 - Cette fonction est utilisée dans le script pour comparer l'entrée de l'utilisateur avec les mots-clés définis et trouver la réponse la plus proche même si les termes exacts ne correspondent pas. Cela améliore la robustesse du chatbot en permettant des correspondances approximatives.

7) Remarques

 - Debugging : Utilisez les outils de développement de votre navigateur (F12) pour observer les logs dans la console et déboguer si nécessaire.

 - Sécurité : Assurez-vous de sécuriser votre application si elle est déployée sur un serveur accessible publiquement.

Auteurs

Créé par John "Bender" Doe /('v')\
