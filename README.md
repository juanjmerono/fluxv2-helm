# Pruebas de Flux V2

## Actualizar Flux a lo bestia

* El namespace flux-system desaparece de modo que lo primero es:

    * Hacer una copia de seguridad de la master key de Sealed Secrets (interesa la activa).

        `kubectl get secret -n flux-system -l sealedsecrets.bitnami.com/sealed-secrets-key -o yaml > master.key`

* Desconecta todos los elementos de flux para que no afecte desinstalar

    `flux get ks`
    `flux suspend ks <name>`

    `flux get sources git`
    `flux suspend source git <name>`

    `flux get sources chart`
    `flux suspend source chart <name>`

    `flux get sources helm`
    `flux suspend source helm <name>`

    `flux get alerts`
    `flux suspend alert <name>`

    `flux get helmreleases`
    `flux suspend helmrelease <name>`

    `flux get image update`
    `flux suspend image update <name>`

    `flux get image repository`
    `flux suspend image repository <name>`

* Desinstala flux

    `flux uninstall --crds`

    * Desplegar la master.key en el nuevo targetNamespace de SealedSecrets.
    * Cambiar el targetNamespace de la HelmRelease de SealedSecrets y reconciliar.
    * No cambia nada porque no hay cambio en la HelmRelease??