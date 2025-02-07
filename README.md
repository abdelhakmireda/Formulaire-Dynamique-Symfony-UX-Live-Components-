# 🌟 Création d'un formulaire dynamique avec Symfony UX Live Components (100% Symfony/Twig, sans JavaScript)

Aujourd'hui, je vais vous guider à travers la création d'un formulaire dynamique et interactif dans votre application Symfony, en utilisant **Symfony UX Live Components**. 
Cette approche, basée exclusivement sur **Symfony et Twig**, vous permettra de gérer des dépendances entre les champs de votre formulaire et d'afficher ou masquer des sections en fonction des interactions de l'utilisateur, **sans avoir à écrire une seule ligne de JavaScript**. 

## 📌 Prérequis

Avant de commencer, assurez-vous d'avoir :
- Symfony installé
- Composer installé
- Une base de données configurée


## ✨ Installation de Symfony UX Live Components

Commençons par installer la librairie **Symfony UX Live Components** à l'aide de Composer :

```bash
composer require symfony/ux-live-component
```

## 🏗️ Création du formulaire (**UtilisateurType**)

La première étape consiste à créer votre formulaire Symfony à l'aide de la commande **make:form**. Dans notre exemple, nous allons créer un formulaire **UtilisateurType** basé sur l'entité **Utilisateur** :

```php
<?php

namespace App\Form;

// ... (use statements)

class UtilisateurType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options): void
    {
        $builder = new DynamicFormBuilder($builder);

        $builder->add('profile', ChoiceType::class, [
            'label' => 'Statut',
            'choices' => [
                'Étudiant' => 'etudiant',
                'Salarié' => 'salarie',
                'Retraité' => 'retraite',
                'Chômeur' => 'chomeur',
            ],
            'expanded' => true,
            'multiple' => false,
        ]);

        // Dépendance pour 'details' en fonction de 'profile'
        $builder->addDependent('details', 'profile', function (DependentField $field, ?string $profile) {
            if ($profile === 'etudiant') {
                $field->add(TextType::class, [
                    'label' => 'École/Université',
                    'mapped' => false, 
                ]);
            } elseif ($profile === 'salarie') {
                $field->add(TextType::class, [
                    'label' => 'Entreprise',
                    'mapped' => false, 
                ]);
            } elseif ($profile === 'retraite') {
                $field->add(IntegerType::class, [
                    'label' => 'Année de Retraite',
                    'mapped' => false, 
                ]);
            } elseif ($profile === 'chomeur') {
                $field->add(TextType::class, [
                    'label' => 'Recherche de Travail',
                    'mapped' => false, 
                ]);
            }
        });
    }

    public function configureOptions(OptionsResolver $resolver): void
    {
        $resolver->setDefaults([
            'data_class' => Utilisateur::class,
        ]);
    }
}
```

⚠️ **Important :** L'option **`mapped => false`** pour les champs dépendants est cruciale. Elle empêche Symfony de mapper directement ces champs à l'entité, car ils sont gérés dynamiquement.

## ⚙️ Création du composant **Live Component** (UtilisateurForm)

Passons à la création du composant **Live Component**. Utilisez la commande :

```bash
php bin/console make:live-component UtilisateurForm
```

Cette commande créera le fichier du composant dans `src/Twig/Components`. Voici le code :

```php
<?php

namespace App\Twig\Components;

use App\Form\UtilisateurType;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Form\FormInterface;
use Symfony\UX\LiveComponent\Attribute\AsLiveComponent;
use Symfony\UX\LiveComponent\ComponentWithFormTrait;
use Symfony\UX\LiveComponent\DefaultActionTrait;

#[AsLiveComponent] // Indique que ce composant est un Live Component
class UtilisateurForm extends AbstractController
{
    use DefaultActionTrait;
    use ComponentWithFormTrait;

    protected function instantiateForm(): FormInterface
    {
        return $this->createForm(UtilisateurType::class);
    }
}
```

⚠️ **Important :** L'attribut `#[AsLiveComponent]` est essentiel pour indiquer que ce composant est un **Live Component**.


## 🎨 Intégration du composant dans Twig (**UtilisateurForm.html.twig**)

Ouvrez le fichier **`templates/components/UtilisateurForm.html.twig`** et ajoutez :

```twig
<div {{ attributes }}> {# L'attribut "attributes" est crucial #}
    {{ form(form) }}
</div>
```

⚠️ **Important :** L'attribut **`{{ attributes }}`** est crucial pour le bon fonctionnement du composant Live Component.


## 🚀 Utilisation du composant

### 🆕 **Création (New) :**
```twig
{{ component('UtilisateurForm') }}
```

### 🔄 **Modification (Edit) :**
```twig
{{ component('UtilisateurForm', { form: form }) }}
```
Où **`form`** est l'instance de votre formulaire pré-rempli.


## 🎯 Conclusion

Avec **Symfony UX Live Components**, la création de **formulaires dynamiques** en **Symfony/Twig** devient un jeu d'enfant, **sans JavaScript** !

🛠️ **Avantages :**
- 🔹 100% **Symfony/Twig**
- 🔹 Dynamique et interactif
- 🔹 Facile à maintenir
- 🔹 Aucune ligne de **JavaScript** nécessaire

📌 J'espère que cette documentation détaillée vous sera utile ! 🚀 
📌 N'hésitez pas à poser vos questions en cas de besoin. 👀

