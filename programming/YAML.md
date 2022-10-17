# YAML 

YAML is a human-readable data-serialization language. It is commonly used for configuration files and in applications where data is being stored or transmitted.

Project Homepage: [YAML](https://yaml.org/) 
Documentation: [YAML Docs](https://yaml.org/spec/1.2.2/)

---

## Anchors

```yml
test_template: &test
  foo: "bar"

bar:
  - *test
```