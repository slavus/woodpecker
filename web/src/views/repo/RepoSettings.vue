<template>
  <FluidContainer>
    <div class="flex border-b items-center pb-4 mb-4 dark:border-gray-600">
      <IconButton icon="back" @click="goBack" />
      <h1 class="text-xl ml-2 text-color">{{ $t('repo.settings.settings') }}</h1>
    </div>

    <Tabs>
      <Tab id="general" :title="$t('repo.settings.general.general')">
        <GeneralTab />
      </Tab>
      <Tab id="secrets" :title="$t('repo.settings.secrets.secrets')">
        <SecretsTab />
      </Tab>
      <Tab id="registries" :title="$t('repo.settings.registries.registries')">
        <RegistriesTab />
      </Tab>
      <Tab id="badge" :title="$t('repo.settings.badge.badge')">
        <BadgeTab />
      </Tab>
      <Tab id="actions" :title="$t('repo.settings.actions.actions')">
        <ActionsTab />
      </Tab>
    </Tabs>
  </FluidContainer>
</template>

<script lang="ts">
import { defineComponent, inject, onMounted, Ref } from 'vue';
import { useI18n } from 'vue-i18n';
import { useRouter } from 'vue-router';

import IconButton from '~/components/atomic/IconButton.vue';
import FluidContainer from '~/components/layout/FluidContainer.vue';
import ActionsTab from '~/components/repo/settings/ActionsTab.vue';
import BadgeTab from '~/components/repo/settings/BadgeTab.vue';
import GeneralTab from '~/components/repo/settings/GeneralTab.vue';
import RegistriesTab from '~/components/repo/settings/RegistriesTab.vue';
import SecretsTab from '~/components/repo/settings/SecretsTab.vue';
import Tab from '~/components/tabs/Tab.vue';
import Tabs from '~/components/tabs/Tabs.vue';
import useNotifications from '~/compositions/useNotifications';
import { useRouteBackOrDefault } from '~/compositions/useRouteBackOrDefault';
import { RepoPermissions } from '~/lib/api/types';

export default defineComponent({
  name: 'RepoSettings',

  components: {
    FluidContainer,
    IconButton,
    Tabs,
    Tab,
    GeneralTab,
    SecretsTab,
    RegistriesTab,
    ActionsTab,
    BadgeTab,
  },

  setup() {
    const notifications = useNotifications();
    const router = useRouter();
    const i18n = useI18n();

    const repoPermissions = inject<Ref<RepoPermissions>>('repo-permissions');
    if (!repoPermissions) {
      throw new Error('Unexpected: "repoPermissions" should be provided at this place');
    }

    onMounted(async () => {
      if (!repoPermissions.value.admin) {
        notifications.notify({ type: 'error', title: i18n.t('repo.settings.not_allowed') });
        await router.replace({ name: 'home' });
      }
    });

    return {
      goBack: useRouteBackOrDefault({ name: 'repo' }),
    };
  },
});
</script>
