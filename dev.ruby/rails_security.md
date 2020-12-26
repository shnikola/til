# Rails HTTP

## force_ssl

`force_ssl` u controlleru će redirectati svaki HTTP request na HTTPS.

`config.force_ssl = true` će postaviti redirect, a usto i postaviti sve cookije i session da budu Secure (da se ne šalju s HTTP requestovima), te podesiti HSTS headere. Detalji se mogu podesiti s `config.ssl_options`

## Rack::Attack

https://github.com/kickstarter/rack-attack

Ograničava requeste koji dolaze na server, prekidajući ih u middlewareu i vraćajući `429 Too Many Requests`.

Podržava različite uvjete:
* `safelist` requestovi uvijek prolaze
* `blocklist` nikad ne prolaze
* `throttle` prolaze ograničen broj puta u nekom intervalu
* `track` propušta requeste i po potrebi ih logira.

## Content Security Policy

U Rails 5.2 možeš koristiti DSL za postavljanje CSP headera. Unutar bloka `Rails.application.config.content_security_policy do |p|` možeš definirati dozvole za različite resurse, npr. `p.default_src :self, :https`, `p.img_src :self, :https, :data`.

U controlleru možeš overridati defaultna pravila s `content_security_policy { |p| ... }`.
