# Projeto Curso - Etapa 1 #

Baseado em https://github.com/mizukami2005/rails_static_sample


## Para Instalar - Etapa 1 ##

1 - Abra o terminal ou prompt de comandos

2 - Abra a sua pasta de projetos ()

3 - Copie o projeto
```
git clone https://github.com/hrangel/rails_static_sample.git
```

4 - Instale
```
cd rails_static_sample
gem install bundler
bundle install
```

5 - Execute
```
rails s
```

6 - Abra no navegador: http://localhost:3000

## Para Criar o Form - Etapa 2 ##

Baseado em:
https://rubyonrailshelp.wordpress.com/2014/01/08/rails-4-simple-form-and-mail-form-to-make-contact-form/

1 - Adicione as gems

Adicione ao gemfile
```
gem 'mail_form'
gem 'simple_form'
```

Exeucte
```
bundle install
```

Instale o Simple Form
```
bundle install
```

2 - Crie models, controller e routes

Novo arquivo: models/contact.rb

```
class Contact < MailForm::Base
  attribute :name,      :validate => true
  attribute :email,     :validate => /\A([\w\.%\+\-]+)@([\w\-]+\.)+([\w]{2,})\z/i
  attribute :message

  # Declare the e-mail headers. It accepts anything the mail method
  # in ActionMailer accepts.
  def headers
    {
      :subject => "My Contact Form",
      :to => "your_email@example.org",
      :from => %("#{name}" <#{email}>)
    }
  end
end
```

Novo arquivo: controllers/contacts_controller.rb

```
class ContactsController < ApplicationController
  def create
    @contact = Contact.new(params[:contact])
    @contact.request = request
    if @contact.deliver
      redirect_to :back, :flash => { :error => "Obrigado por enviar sua mensagem!" }
    else
      redirect_to :back, :flash => { :error => "Mensagem não pode ser enviada." }
    end
  end
end
```

Adicione ao routes.rb
```
resources "contacts", only: [:create]
```

3 - Adicione o form

Novo arquivo: views/static_pages/_contact_form.html

```
<div align="center">
  <h3>Envie uma mensagem</h3>

  <%= simple_form_for @contact, :html => {:class => 'form-horizontal' } do |f| %>
    <%= f.input :name, :required => true %>
    <%= f.input :email, :required => true %>
    <%= f.input :message, :as => :text, :required => true %>
    <div>
      </br>
      <%= f.button :submit, 'Send message', :class=> "btn btn-primary" %>
    </div>
  <% end %>
</div>
```

Adicione partial ao arquivo home
```
<div>
  <% flash.each do |key, value| %>
    <div>
      <a href="#" class="<%=key.to_s %>" data-dismiss="alert">×</a>
        <%= value %>
    </div>
  <% end %>
</div>

<%= render 'static_pages/_contact_form' %>
```

Configure o css
```
.hidden { display: none; }

.alert-error {
background-color: #f2dede;
border-color: #eed3d7;
color: #b94a48;
text-align: left;
}

.alert-alert {
background-color: #f2dede;
border-color: #eed3d7;
color: #b94a48;
text-align: left;
}

.alert-success {
background-color: #dff0d8;
border-color: #d6e9c6;
color: #468847;
text-align: left;
}

.alert-notice {
background-color: #dff0d8;
border-color: #d6e9c6;
color: #468847;
text-align: left;
}
```

4 - Configure mailer

```
ActionMailer::Base.smtp_settings = {
  :enable_starttls_auto => true,
  :address        => 'smtp.gmail.com',
  :port           => 587,
  :domain         => 'aaaaaa.com',
  :authentication => :plain,
  :user_name      => "aaaa@aaaaaa.cool",
  :password       => "aaaaa"
}
```