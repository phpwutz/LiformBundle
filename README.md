LiformBundle
============

Bundle to serialize Symfony Forms into [JSON schema](http://json-schema.org/) that integrates the library [Liform](https://github.com/Limenius/Liform) with Symfony. For use with [liform-react](https://github.com/Limenius/liform-react) or [json-editor](https://github.com/jdorn/json-editor), or any other form generator based in json-schema.

It is very annoying to maintain Symfony forms that match forms in a client technology, such as JavaScript. It is also annoying to maintain a documentation of such forms. And error prone.

LiformBundle generates a JSON schema representation, that serves as documentation and can be used to validate your data and, if you want, to generate forms using a generator.

## Installation

First and foremost, note that you have a complete example with React, Webpack and Symfony Standard Edition at [Limenius/symfony-react-sandbox](https://github.com/Limenius/symfony-react-sandbox) ready for you, which includes an example of usage of this bundle.

Feel free to clone it, run it, experiment, and copy the pieces you need to your project. Being this bundle a frontend-oriented bundle, you are expected to have a compatible frontend setup.

### Step 1: Download the Bundle

Open a command console, enter your project directory and execute the
following command to download the latest stable version of this bundle:

    $ composer require limenius/liform-bundle

This command requires you to have Composer installed globally, as explained
in the *installation chapter* of the Composer documentation.

### Step 2: Enable the Bundle

Then, enable the bundle by adding the following line in the `app/AppKernel.php`
file of your project:

```php
// app/AppKernel.php

// ...
class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            // ...

            new Limenius\LiformBundle\LimeniusLiformBundle(),
        );

        // ...
    }

    // ...
}
```

## Usage

Serializing a form into JSON Schema:

```php
        $form = $this->createForm(CarType::class, $car, ['csrf_protection' => false]);
        $schema = json_encode($this->get('liform')->transform($form));
```

And `$schema` will contain a JSON Schema representation such as:

```js
{  
   "title":null,
   "properties":{  
      "name":{  
         "type":"string",
         "title":"Name",
         "propertyOrder":1
      },
      "color":{  
         "type":"string",
         "title":"Color",
         "attr":{  
            "placeholder":"444444"
         },
         "default":"444444",
         "description":"3 hexadecimal digits",
         "propertyOrder":2
      },
      "drivers":{  
         "type":"array",
         "title":"hola",
         "items":{  
            "title":"Drivers",
            "properties":{  
               "firstName":{  
                  "type":"string",
                  "propertyOrder":1
               },
               "familyName":{  
                  "type":"string",
                  "propertyOrder":2
               }
            },
            "required":[  
               "firstName",
               "familyName"
            ],
            "type":"object"
         },
         "propertyOrder":3
      }
   },
   "required":[  
      "name",
      "drivers"
   ]
}

```

## Serializing initial values

This bundle registers a serializer to serialize a `FormView` (you can create one with `$form->createView()`) into an array of initial values. Just do in your action:

```php
$serializer = $this->get('serializer');
$initialValues = $serializer->normalize($form->createView()),
```

To obtain an array of initial values that match your json-schema.


## Serializing errors


This bundle registers a serializer to serialize forms with errors into an array. This part was shameless taken from [FOSRestBundle](https://github.com/FriendsOfSymfony/FOSRestBundle/blob/master/Serializer/Normalizer/FormErrorNormalizer.php). Just do in your action:

```php
$serializer = $this->get('serializer');
$errors = $serializer->normalize($form),
```

To obtain an array with the errors of your form. Liform-react, if you are using it, can understand this format.

## License

This bundle is under the MIT license. See the complete license in the bundle:

    LICENSE.md

## Acknoledgements

The technique for transforming forms using resolvers and reducers is inspired on [Symfony Console Form](https://github.com/matthiasnoback/symfony-console-form)
