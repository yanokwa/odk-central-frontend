<!--
Copyright 2020 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <div id="public-link-list">
    <div class="heading-with-button">
      <button type="button" class="btn btn-primary"
        @click="showModal('create')">
        <span class="icon-plus-circle"></span>{{ $t('action.create') }}&hellip;
      </button>
      <p>
        <i18n :tag="false" path="heading[0].full">
          <template #state>
            <router-link :to="projectPath('form-access')">{{ $t('heading[0].state') }}</router-link>
          </template>
        </i18n>
        &nbsp;
        <i18n :tag="false" path="moreInfo.clickHere.full">
          <template #clickHere>
            <doc-link to="central-submissions/#public-access-links">{{ $t('moreInfo.clickHere.clickHere') }}</doc-link>
          </template>
        </i18n>
      </p>
      <i18n tag="p" path="heading[1].full">
        <template #clickHere>
          <a href="#" @click.prevent="showModal('submissionOptions')">{{ $t('heading[1].clickHere') }}</a>
        </template>
      </i18n>
    </div>

    <public-link-table :highlighted="highlighted" @revoke="showRevoke"/>
    <loading :state="$store.getters.initiallyLoading(['publicLinks'])"/>
    <p v-if="publicLinks != null && publicLinks.length === 0"
      class="empty-table-message">
      {{ $t('emptyTable') }}
    </p>

    <public-link-create v-bind="create" @hide="hideModal('create')"
      @success="afterCreate"/>
    <project-submission-options v-bind="submissionOptions"
      @hide="hideModal('submissionOptions')"/>
    <public-link-revoke v-bind="revoke" @hide="hideRevoke"
      @success="afterRevoke"/>
  </div>
</template>

<script>
import DocLink from '../doc-link.vue';
import Loading from '../loading.vue';
import PublicLinkCreate from './create.vue';
import PublicLinkRevoke from './revoke.vue';
import PublicLinkTable from './table.vue';
import modal from '../../mixins/modal';
import routes from '../../mixins/routes';
import { apiPaths } from '../../util/request';
import { loadAsync } from '../../util/async-components';
import { noop } from '../../util/util';
import { requestData } from '../../store/modules/request';

export default {
  name: 'PublicLinkList',
  components: {
    DocLink,
    Loading,
    ProjectSubmissionOptions: loadAsync('ProjectSubmissionOptions'),
    PublicLinkCreate,
    PublicLinkRevoke,
    PublicLinkTable
  },
  mixins: [modal({ submissionOptions: 'ProjectSubmissionOptions' }), routes()],
  props: {
    projectId: {
      type: String,
      required: true
    },
    xmlFormId: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      // The id of the highlighted public link
      highlighted: null,
      // Modals
      create: {
        state: false
      },
      submissionOptions: {
        state: false
      },
      revoke: {
        state: false,
        publicLink: null
      }
    };
  },
  // The component does not assume that this data will exist when the component
  // is created.
  computed: requestData(['publicLinks']),
  created() {
    this.fetchData(false);
  },
  methods: {
    fetchData(resend) {
      this.$store.dispatch('get', [{
        key: 'publicLinks',
        url: apiPaths.publicLinks(this.projectId, this.xmlFormId),
        resend
      }]).catch(noop);
      this.highlighted = null;
    },
    showRevoke(publicLink) {
      this.revoke.publicLink = publicLink;
      this.showModal('revoke');
    },
    hideRevoke() {
      this.hideModal('revoke');
      this.revoke.publicLink = null;
    },
    afterCreate(publicLink) {
      this.fetchData(true);
      this.hideModal('create');
      this.$alert().success(this.$t('alert.create'));
      this.highlighted = publicLink.id;
    },
    afterRevoke(publicLink) {
      this.fetchData(true);
      this.hideRevoke();
      this.$alert().success(this.$t('alert.revoke', publicLink));
    }
  }
};
</script>

<i18n lang="json5">
{
  "en": {
    "action": {
      "create": "Create Public Access Link",
    },
    "heading": [
      {
        "full": "Anyone with a Public Access Link can fill out this Form in a web browser. You can create multiple Links to track different distributions of the Form, to limit how long a specific group of people has access to the Form, and more. These links will only work if the Form is in the Open {state}.",
        "state": "state"
      },
      {
        "full": "Public Links are intended for self-reporting. If you are working with data collectors who need to submit the same Form multiple times, {clickHere} for other options.",
        "clickHere": "click here"
      }
    ],
    "emptyTable": "There are no Public Access Links for this Form.",
    "alert": {
      "create": "Success! Your Public Access Link has been created and is now live. Copy it below to distribute it.",
      "revoke": "The Public Access Link “{displayName}” was revoked successfully. No further Submissions will be accepted using this Link."
    }
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "cs": {
    "action": {
      "create": "Vytvořit veřejně přístupný odkaz"
    },
    "heading": [
      {
        "full": "Kdokoli s odkazem na veřejný přístup může vyplnit tento formulář ve webovém prohlížeči. Můžete vytvořit více odkazů pro sledování různých distribucí formuláře, abyste omezili, jak dlouho má určitá skupina lidí přístup k formuláři a další. Tyto odkazy budou fungovat, pouze pokud je formulář ve {state} Open.",
        "state": "stavu"
      },
      {
        "full": "Veřejné odkazy jsou určeny pro vlastní reportování. Pokud pracujete se sběrači dat, kteří potřebují zaslat stejný formulář vícekrát, {clickHere} pro jiné možnosti.",
        "clickHere": "klikněte zde"
      }
    ],
    "emptyTable": "Pro tento formulář neexistují žádné veřejně přístupné odkazy.",
    "alert": {
      "create": "Úspěch! Váš veřejně přístupný odkaz byl vytvořen a je nyní aktivní. Zkopírujte jej níže a šiřte ho.",
      "revoke": "Veřejně přístupný odkaz „{displayName}“ byl úspěšně odebrán. Pomocí tohoto odkazu nebudou přijata žádná další podání."
    }
  },
  "de": {
    "action": {
      "create": "Öffentlichen Zugangslink erstellen"
    },
    "heading": [
      {
        "full": "Jeder mit einem öffentlichen Zugangslink kann dieses Formular in einem Webbrowser ausfüllen. Sie können mehrere Links erstellen, um z.B. unterschiedliche Verteilungen des Formulars nachzuverfolgen oder zu begrenzen wie lange eine spezifische Gruppe von Menschen Zugriff auf das Formular hat. Diese Links funktionieren nur, wenn das Formular den {state} Offen hat.",
        "state": "Status"
      },
      {
        "full": "Öffentliche Links sind für das Selbstbeantworten gedacht. Wenn Sie mit Datensammlern arbeiten, die dasselbe Formular mehrfach übermitteln müssen, {clickHere} für andere Optionen.",
        "clickHere": "klicken Sie hier"
      }
    ],
    "emptyTable": "Es gibt keine öffentlichen Zugangslinks für dieses Formular.",
    "alert": {
      "create": "Ihr öffentlicher Zugangslink wurde erfolgreich erstellt und ist jetzt live. Kopieren Sie ihn unten, um ihn zu verteilen.",
      "revoke": "Der öffentliche Zugangslink \"{displayName}\" wurde erfolgreich widerrufen. Keine weiteren Übermittlungen über diesen Link werden akzeptiert."
    }
  },
  "es": {
    "action": {
      "create": "Crear enlace de acceso público"
    },
    "heading": [
      {
        "full": "Cualquier persona con un enlace de acceso público puede rellenar este formulario en un navegador web. Puede crear múltiples enlaces para rastrear diferentes distribuciones del formulario, para limitar el tiempo que un grupo específico de personas tiene acceso al Formulario, y más. Estos enlaces sólo funcionarán si el formulario está en el {state} abierto.",
        "state": "estado"
      },
      {
        "full": "Los enlaces públicos están pensados para autoinformarse. Si usted se encuentra trabajando con recolectores de datos que necesitan enviar el mismo formulario varias veces, {clickHere} para otras opciones.",
        "clickHere": "haga clic aquí"
      }
    ],
    "emptyTable": "No hay enlaces de acceso público para este formulario.",
    "alert": {
      "create": "¡Operación exitosa! Su enlace de acceso público ha sido creado y está ahora en vivo. Cópialo abajo para distribuirlo.",
      "revoke": "El enlace de acceso público \"{displayName}\" fue revocado con éxito. No se aceptarán más envíos a través de este enlace."
    }
  },
  "fr": {
    "action": {
      "create": "Créer lien d'accès public"
    },
    "heading": [
      {
        "full": "N'importe qui avec ce lien pourra remplir ce formulaire depuis leur navigateur. Vous pouvez créer plusieurs liens pour suivre d'où viennent vos répondants, pour limiter le temps d'accès au formulaire et ainsi de suite. Ces liens ne fonctionneront que si le formulaire et dans {state} \"ouvert\".",
        "state": "l'état"
      },
      {
        "full": "Les liens d'accès public sont conçus pour l'auto-évaluation. Si vous travaillez avec des collecteurs de données qui doivent soumettre le même formulaire plusieurs fois, {clickHere} pour d'autres méthodes d'envoi de données.",
        "clickHere": "cliquez ici"
      }
    ],
    "emptyTable": "Il n'y a aucun lien d'accès public pour ce formulaire.",
    "alert": {
      "create": "Succès! Votre lien d'accès public a été créé et est désormais accessible. Copiez le ci-dessous pour le distribuer.",
      "revoke": "Le lien d'accès public \"{displayName}\" a été révoqué avec succès. Aucune soumission ne sera acceptée depuis ce lien."
    }
  },
  "id": {
    "action": {
      "create": "Buat Tautan Akses Publik"
    },
    "heading": [
      {
        "full": "Siapapun dengan Tautan Akses Publik dapat mengisi formulir lewat web browser. Anda dapat membuat beberapa tautan untuk melacak pembagian formulir, membatasi seberapa lama kelompok tertentu memiliki akses formulir, dan banyak lagi. Tautan-tautan ini hanya akan berfungsi jika form berada dalam {state} Terbuka.",
        "state": "status"
      },
      {
        "full": "Tautan Publik dimaksudkan untuk laporan pribadi. Apabila Anda bekerja dengan pengumpul data yang butuh mengirimkan beberapa data dalam satu formulir yang sama, {clickHere} untuk pilihan lain.",
        "clickHere": "klik di sini"
      }
    ],
    "emptyTable": "Tidak ada Tautan Akses Publik untuk formulir ini.",
    "alert": {
      "create": "Berhasil! Tautan Akses Publik telah dibuat dan aktif. Salin tautan di bawah untuk disebar.",
      "revoke": "Tautan Akses Publik \"{displayName}\" telah berhasil dicabut. Tidak akan ada lagi kiriman data baru yang diterima lewat tautan ini."
    }
  }
}
</i18n>
