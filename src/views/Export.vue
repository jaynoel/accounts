<template></template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import { ParsedSimpleRequest } from '../lib/RequestTypes';
import { SimpleRequest } from '@nimiq/keyguard-client';
import { WalletStore } from '@/lib/WalletStore';
import { Static } from '../lib/StaticStore';

@Component
export default class Export extends Vue {
    @Static private request!: ParsedSimpleRequest;

    public async created() {
        const wallet = await WalletStore.Instance.get(this.request.walletId);
        if (!wallet) {
            this.$rpc.reject(new Error('Account ID not found'));
            return;
        }

        const request: SimpleRequest = {
            appName: this.request.appName,
            keyId: this.request.walletId,
            keyLabel: wallet.label,
        };

        const client = this.$rpc.createKeyguardClient();
        client.export(request);
    }
}
</script>
