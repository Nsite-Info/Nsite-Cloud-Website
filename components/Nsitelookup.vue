<script setup>
const { t } = useI18n({ useScope: "local" });

import { ExclamationTriangleIcon } from '@heroicons/vue/20/solid'

import data from "~/config/setup";

// import design from "~/config/design";

const searchTerm = ref('');

const hex = ref('');


import { bech32 } from 'bech32';

import debounce from 'debounce';
import NDK from '@nostr-dev-kit/ndk'; // Corrected import statement

// Utility functions
const transparentPixel = 'data:image/gif;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=';  // 1x1 transparent pixel
const handleImageError = (event) => {
  event.target.src = transparentPixel;
};

// Nostr relays
const relays = [
  'wss://nostr.wine',
  'wss://search.nos.today',
  'wss://relay.nostr.band',
  'wss://relay.noswhere.com',
];

// Initialize NDK with relays
const ndk = new NDK({ explicitRelayUrls: relays });

// State variables
// const searchTerm = ref('');
const users = ref([]);
let currentSubscription = null;  // Track the current subscription

// Function to clear the users array
const clearUsers = () => {
  users.value = [];
};

// Search function using NDK
const searchUsers = async (query) => {
  if (query.length < 2) {
    clearUsers();
    return;
  }

  const newUsers = new Set();  // Use a Set to handle duplicates
  const filter = { kinds: [0], search: query }; // Kinds [0] for metadata

  // Unsubscribe from previous search if it exists
  if (currentSubscription) {
    currentSubscription.stop();  // Stop the previous subscription
  }

  // Create a new subscription
  currentSubscription = ndk.subscribe(filter, { relays });

  currentSubscription.on('event', (event) => {
    try {
      const metadata = JSON.parse(event.content);
      const user = {
        pubkey: event.pubkey,
        npub: event.pubkey,  // You can encode this if needed
        name: metadata.name || 'Unknown',
        picture: metadata.picture,
      };

      // Add to Set to avoid duplicates
      newUsers.add(JSON.stringify(user));  // Serialize to use Set

      // Limit to 10 results
      if (newUsers.size > 10) {
        const entries = Array.from(newUsers).slice(0, 10);
        users.value = entries.map(JSON.parse);  // Deserialize back to objects
      } else {
        users.value = Array.from(newUsers).map(JSON.parse);  // Update users
      }
    } catch (error) {
      console.error('Error parsing event:', error);
    }
  });

  // Close the connection after 10 seconds
  setTimeout(() => {
    if (currentSubscription) {
      currentSubscription.stop();  // Stop the subscription
      currentSubscription = null;
    }
  }, 10000); // 10 seconds
};

// Debounced search function
const debouncedSearch = debounce(() => searchUsers(searchTerm.value), 450);

// Handle user selection
const selectUser = async (user) => {
  try {
    console.log(user.name)
    const response = await fetch(`https://register.sov.biz/nostrnip5/api/v1/domain/eaz5cgZEbGd8xGv9ZzymtN/nostr.json?name=${user.name}`);

    const data = await response.json();
    const hex = data.names?.[user];

    if (hex && hex !== "Not found") {
      console.log("we did find a username routing to the username " + hex.value)
      window.location.href = "https://"+ convertHexToNpub(user.npub) + ".nsite.cloud";

      // Redirect to npub subdomain
      // window.location.href = "https://"+ convertHexToNpub(user.npub) + ".cypher.space";
    } else {
      // Redirect to user subdomain if no match
      console.log("we didn't find a username falling back to npub route ")
      window.location.href = "https://"+ convertHexToNpub(user.npub) + ".nsite.cloud";

      // window.location.href = "https://"+ user.name + ".cypher.space";
    }
  } catch (error) {
    // Handle fetch or processing errors by redirecting
    console.log("Error Handle Falling back to npub route ")
    window.location.href = "https://"+ convertHexToNpub(user.npub) + ".nsite.cloud";

    // window.location.href = "https://"+ convertHexToNpub(user.npub) + ".cypher.space";
  }
};


// Watch for changes in search term and unsubscribe if needed
watch(searchTerm, (newVal) => {
  if (newVal.length < 2 && currentSubscription) {
    currentSubscription.stop();
    currentSubscription = null;
    clearUsers();
  }
});

// Connect to NDK relays when the component is created
ndk.connect();


const hexToByteArray = (hex) => {
  const bytes = [];
  for (let i = 0; i < hex.length; i += 2) {
    bytes.push(parseInt(hex.substr(i, 2), 16));
  }
  return bytes;
};

const convertHexToNpub = (hex) => {
  try {
    const byteArray = hexToByteArray(hex);
    const words = bech32.toWords(byteArray); // Convert byte array to Bech32 words
    const npub = bech32.encode('npub', words); // Encode using the 'npub' prefix
    return npub;
  } catch (error) {
    console.error('Error converting hex to npub:', error);
    return null;
  }
};

const lookupNpub = async (user, domain) => {
  try {
    const response = await fetch(
      `https://register.sov.biz/nostrnip5/api/v1/domain/eaz5cgZEbGd8xGv9ZzymtN/nostr.json?name=${user}`
    );
    if (response.ok) {
      const data = await response.json();
      console.log("We Found a username at register" + data.names[user]);

      const hex = data.names[user];

      if (hex !== "Not found") {
        const byteArray = hexToByteArray(hex);
        const words = bech32.toWords(byteArray);
        const npub = bech32.encode('npub', words);
        
        window.location.href = `https://${npub}.nsite.cloud`;

        console.log(`npub: ${npub}`);
      } else {
        console.log('Hex not found');
      }
    } else {
      console.log('Error fetching data');
    }
  } catch (error) {
    console.log('Error fetching data:', error);
  }
};


const resolve = () => {
  // Trim spaces from the input value
  searchTerm.value = searchTerm.value.trim();

  const inputLength = searchTerm.value.length;
  if (searchTerm.value == '') {
    console.log("It's an empty world");
    alert("Sovereign Space can't be Empty")
  } 
  else if (searchTerm.value.includes('@')) {
    console.log("Slug contains @ symbol.");
    try {
      const [user, domain] = searchTerm.value.split("@");
      lookupNpub(user, domain).then(() => {});
    } catch (error) {
      console.error("Failed to fetch user profile:", error);
    }
  } else if (searchTerm.value.startsWith('npub')) {
    console.log("Slug starts with npub.");
    try {
      window.location.href = `https://${searchTerm.value}.nsite.cloud`;
    } catch (error) {
      console.error("Failed to fetch user profile:", error);
    }
  } else if (inputLength < 25) {
    console.log("Input is less than 25 characters.");
    try {
      window.location.href = `https://${searchTerm.value}.nsite.cloud`;
    } catch (error) {
      console.error("Error processing input less than 25 characters:", error);
    }
  } else if (inputLength > 25) {
    console.log("Input is more than 25 characters.");
    try {
      window.location.href = `https://nsite.info/${searchTerm.value}`;
    } catch (error) {
      console.error("Error processing input more than 25 characters:", error);
    }
  } else {
    console.log("Input does not match any conditions.");
  }
};

// Function to handle the Enter key press
const handleKeyPress = (event) => {
  if (event.key === 'Enter') {
    resolve();
  }
};
</script>

<template>
  <div class="relative w-full flex flex-col">
    
    <!-- MAIN CONTENT AREA -->
    <div class="relative z-10 flex-grow mt-6 md:mt-6 px-6">
      <div class="mx-auto max-w-4xl py-0 sm:py-6 lg:py-6">
        <div class="text-center">

          <Warning />
          
          <h1 v-if="!data.logo" class=" text-4xl font-semibold tracking-tight text-white sm:text-8xl">{{data.textlogo}}</h1>
          
          <!-- Title -->
          <h1 class="text-2xl font-bold tracking-tight text-white sm:text-3xl my-0">{{ t('bitcoinWebstackTitle') }} <span class="text-4xl"></span></h1>

          <!-- Search Input -->
          <input 
            type="text" 
            v-model="searchTerm"
            :placeholder="t('search')" 
            @input="debouncedSearch"
            @keydown="handleKeyPress"
            class="dark:text-black w-full h-16 rounded-2xl mt-6 md:mt-12 px-4 shadow-xl shadow-white/10"
          />
          
          <!-- Search Results -->
          <ul v-if="users.length" class="divide-y divide-gray-200 rounded-2xl bg-gray-900 max-h-72 overflow-y-auto mt-6">
            <li
              v-for="user in users"
              :key="user.pubkey"
              @click="selectUser(user)"
              class="flex items-center p-2 cursor-pointer hover:bg-gray-100 transition hover:text-black text-white"
            >
              <img
                :src="user.picture || transparentPixel"
                @error="handleImageError"
                class="w-10 h-10 mr-2 rounded-full"
              />
              <span class="flex-1 truncate">{{ user.name }} {{ convertHexToNpub(user.npub) }}</span>
            </li>
          </ul>

          <button @click="resolve" class="mt-6 h-12 bg-purple-600 hover:bg-purple-700 text-base border-2 text-black text-white font-bold py-2 px-4 rounded shadow-xl shadow-white/20">{{ t('button') }}</button>


        </div>
      </div>
    </div>
  </div>
</template>


<i18n lang="json">
  {
    "da": {
      "search": "Søg på Navn, Npub, Nip-05 eller Hash",
      "button": "LAD OS GÅ 🔥",
      "line1": "Demo-legitimationsoplysninger opdaget!",
      "line2": "Skift LNaddress, btcaddress og orderwebhook i ",
      "line3": "en åben Discord-kanal",
      "line4": "og midler sendes til en Prism-tegnebog for bidragsydere.",
      "line5": "Du kan også tage et kig på",
      "line6": "tjeklisten",
      "line7": "dette er ting, du stadig bør mærke / tilpasse før du sætter i produktion.",
      "line8": "du kan se det aktuelle WIP lokale admin UI",
      "adminPanel": "Admin Panel",
      "bitcoinWebstackTitle": "Søg Suverænt Rum",
      "proofOfWorkPudding": "Velkommen til vores digitale hjørne udforsk indhold og produkter",
      "buildBitcoinNostrDescription": "Byg Bitcoin & Nostr Ecosystem virksomheder og udnyt den bedste måde at farme bitcoin og stable sats! Leverer varer, tjenester og løsninger til bitcoin adoption og integration.",
      "viewProjectsButton": "Se Butik",
      "getStartedLink": "Begynd at læse",
      "startHere": "Start her for at oprette en profil."
    },
    "de": {
      "search": "Suchen Sie nach Name, Npub, Nip-05 oder Hash",
      "button": "LOS GEHT'S 🔥",
      "line1": "Demo-Zugangsdaten erkannt!",
      "line2": "Ändern Sie LNaddress, btcaddress und orderwebhook in der ",
      "line3": "einen offenen Discord-Kanal",
      "line4": "und Gelder werden an eine Prism-Wallet für Mitwirkende gesendet.",
      "line5": "Sie können auch einen Blick werfen auf",
      "line6": "die Checkliste",
      "line7": "das sind Dinge, die Sie noch kennzeichnen / anpassen sollten, bevor Sie in Produktion gehen.",
      "line8": "Sie können die aktuelle WIP lokale Admin-Oberfläche einsehen",
      "adminPanel": "Admin-Panel",
      "bitcoinWebstackTitle": "Suchen Suverænt Rum",
      "proofOfWorkPudding": "Willkommen in unserer digitalen Ecke erkunden Sie Inhalte und Produkte",
      "buildBitcoinNostrDescription": "Bauen Sie Bitcoin & Nostr Ökosystem-Unternehmen und nutzen Sie den besten Weg, um Bitcoin zu farmen und Sats zu stapeln! Bereitstellung von Waren, Dienstleistungen und Lösungen für die Bitcoin-Adoption und Integration.",
      "viewProjectsButton": "Shop ansehen",
      "getStartedLink": "Beginnen Sie mit dem Lesen",
      "startHere": "Starten Sie hier, um ein Profil zu erstellen."
    },
    "es": {
      "search": "Busca por Nombre, Npub, Nip-05 o Hash",
      "button": "VAMOS 🔥",
      "line1": "¡Credenciales de demostración detectadas!",
      "line2": "Cambia LNaddress, btcaddress y orderwebhook en el archivo ",
      "line3": "un canal abierto de Discord",
      "line4": "y los fondos se envían a una billetera Prism para colaboradores.",
      "line5": "También puedes echar un vistazo a",
      "line6": "la lista de verificación",
      "line7": "estas son cosas que todavía deberías personalizar antes de poner en producción.",
      "line8": "puedes ver la UI de administración local WIP actual",
      "adminPanel": "Panel de Administración",
      "bitcoinWebstackTitle": "Buscar Espacio Soberano",
      "proofOfWorkPudding": "Bienvenido a nuestro rincón digital, explora contenido y productos",
      "buildBitcoinNostrDescription": "Construye negocios del ecosistema Bitcoin & Nostr y utiliza la mejor manera de farmear bitcoin y apilar sats! Ofreciendo bienes, servicios y soluciones para la adopción e integración de bitcoin.",
      "viewProjectsButton": "Ver Tienda",
      "getStartedLink": "Comenzar a leer",
      "startHere": "Comienza aquí para crear un perfil."
    },
    "en": {
      "search": "Search on Name, Npub, Nip-05 or Hash",
      "button": "LETS GO 🔥",
      "line1": "Demo Credentials Detected!",
      "line2": "Change the LNaddress, btcaddress and orderwebhook in the ",
      "line3": "an open discord channel",
      "line4": "and funds are sent to a prism wallet for contributors.",
      "line5": "You can also have a look at",
      "line6": "the checklist ",
      "line7": "these are things you should still brand / customize before putting in production.",
      "line8": "you can view the current WIP local admin UI ",
      "adminPanel": "Admin Panel",
      "bitcoinWebstackTitle": "Search Sovereign Space",
      "proofOfWorkPudding": "Welcome to our digital corner, explore content and products",
      "buildBitcoinNostrDescription": "Build Bitcoin & Nostr Ecosystem Businesses and utilize the best way to farm bitcoin and stack sats! Providing goods, services and solutions for bitcoin adoption and integrations.",
      "viewProjectsButton": "View Shop",
      "getStartedLink": "Start Reading",
      "startHere": "Start here to create a profile."
    },
    "fr": {
      "search": "Recherchez par Nom, Npub, Nip-05 ou Hash",
      "button": "ALLONS-Y 🔥",
      "line1": "Identifiants de démo détectés !",
      "line2": "Changez LNaddress, btcaddress et orderwebhook dans le fichier ",
      "line3": "un canal Discord ouvert",
      "line4": "et les fonds sont envoyés à un portefeuille Prism pour les contributeurs.",
      "line5": "Vous pouvez également jeter un œil à",
      "line6": "la liste de contrôle",
      "line7": "ce sont des choses que vous devriez encore personnaliser avant de mettre en production.",
      "line8": "vous pouvez consulter l'interface administrateur locale WIP actuelle",
      "adminPanel": "Panneau d'Admin",
      "bitcoinWebstackTitle": "Rechercher Espace Souverain",
      "proofOfWorkPudding": "Bienvenue dans notre coin numérique, explorez le contenu et les produits",
      "buildBitcoinNostrDescription": "Construisez des entreprises de l'écosystème Bitcoin & Nostr et utilisez la meilleure façon de farmer des bitcoins et d'empiler des sats! Fournissant des biens, des services et solutions pour l'adoption et l'intégration du bitcoin.",
      "viewProjectsButton": "Voir la Boutique",
      "getStartedLink": "Commencer à lire",
      "startHere": "Commencez ici pour créer un profil."
    },
    "nl": {
      "search": "Zoeken op Naam, Npub, Nip-05 of Hash",
      "button": "LATEN WE GAAN 🔥",
      "line1": "Demo-credentials gedetecteerd!",
      "line2": "Wijzig LNaddress, btcaddress en orderwebhook in het ",
      "line3": "een open Discord-kanaal",
      "line4": "en fondsen worden verzonden naar een Prism-portemonnee voor bijdragers.",
      "line5": "Je kunt ook kijken naar",
      "line6": "de checklist",
      "line7": "dit zijn dingen die je nog moet brandmerken / aanpassen voordat je in productie gaat.",
      "line8": "u kunt de huidige WIP lokale admin UI bekijken",
      "adminPanel": "Admin Paneel",
      "bitcoinWebstackTitle": "Zoek Soevereine Ruimte",
      "proofOfWorkPudding": "Welkom in onze digitale hoek, verken inhoud en producten",
      "buildBitcoinNostrDescription": "Bouw Bitcoin & Nostr Ecosysteem Bedrijven en gebruik de beste manier om bitcoin te farmen en sats te stapelen! Het leveren van goederen, diensten en oplossingen voor bitcoin adoptie en integratie.",
      "viewProjectsButton": "Bekijk Winkel",
      "getStartedLink": "Begin met lezen",
      "startHere": "Begin hier om een profiel te maken."
    },
    "pt": {
      "search": "Pesquisar por Nome, Npub, Nip-05 ou Hash",
      "button": "VAMOS LÁ 🔥",
      "line1": "Credenciais de demonstração detectadas!",
      "line2": "Altere o LNaddress, btcaddress e orderwebhook no ",
      "line3": "um canal aberto do Discord",
      "line4": "e os fundos são enviados para uma carteira Prism para colaboradores.",
      "line5": "Você também pode dar uma olhada na",
      "line6": "lista de verificação",
      "line7": "estas são coisas que você ainda deve personalizar antes de colocar em produção.",
      "line8": "você pode ver a interface de administração local WIP atual",
      "adminPanel": "Painel Admin",
      "bitcoinWebstackTitle": "Buscar Espaço Soberano",
      "proofOfWorkPudding": "Bem-vindo ao nosso canto digital, explore conteúdo e produtos",
      "buildBitcoinNostrDescription": "Construa negócios do ecossistema Bitcoin & Nostr e utilize a melhor maneira de minerar bitcoin e acumular sats! Fornecendo bens, serviços e soluções para a adoção e integração do bitcoin.",
      "viewProjectsButton": "Ver Loja",
      "getStartedLink": "Comece a Ler",
      "startHere": "Comece aqui para criar um perfil."
    }
  }
  </i18n>
  