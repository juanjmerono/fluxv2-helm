# Pruebas de Flux V2

## Actualizar Flux a lo bestia

* El namespace flux-system desaparece de modo que lo primero es:

    * Hacer una copia de seguridad de la master key de Sealed Secrets (interesa la activa).

        `kubectl get secret -n flux-system -l sealedsecrets.bitnami.com/sealed-secrets-key -o yaml > master.key`

    * Hacer copias o recuperar los GITLAB_USER/TOKEN usados para desplegar originalmente.

        `kubectl get secret -n flux-system flux-system -o yaml > gitlab.key`

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

* Descarga el nuevo cliente y renombra el viejo como flux063
* Instala flux vas a necesitar el gitlab user/token que usabas

    `flux bootstrap .....`

* Despliega la master.key en el nuevo flux-system
* Copia elementos del flux-system y de la vieja carpeta a la nueva
