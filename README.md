# fluxv2-helm

* Mover Sealed Secrets FUERA del namespace flux-system
    * Hacer una copia de seguridad de la master key (interesa la activa)

        `kubectl get secret -n flux-system -l sealedsecrets.bitnami.com/sealed-secrets-key -o yaml > master.key`

    * Desplegar la master.key en el nuevo targetNamespace de SealedSecrets
    * Cambiar el targetNamespace de la HelmRelease de SealedSecrets y reconciliar