# DJANGO_USER

django_extend_user_models
1- Models
# models.py

# Import

        from django.contrib.auth.models import User
        from django.db.models.signals import post_save
        from django.dispatch import receiver




#----------------------------------- USER -->> PROFILE --------------------------------#

    class Profile(models.Model):
        # Appel de user

        user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='profile')  # 1 user <---> 1 profil

        # Champs suplementaires

        contacts = models.CharField(max_length=30, null=True)
        image = models.ImageField(upload_to='profile/', default='useravatar.png')
        birth_date = models.DateField(null=True)

        # Initialisation a la creation

        @receiver(post_save, sender=User)
        def create_user_profile(sender, instance, created, **kwargs):
            if created:
                Profile.objects.create(user=instance)

        @receiver(post_save, sender=User)
        def save_user_profile(sender, instance, created, **kwargs):

            instance.profile.save()
2 enregistrement

     # User
     user = User(username =username, first_name = first_name, last_name = last_name, email = email)
     user.save()
    # Profile
    
    user.profile.contacts = contacts
    user.profile.image = img
    user.profile.birth_date = birth_date

    user.save()
    
    # Password
    
    user.password = password
    user.set_password(user.password)
    user.save()




3 login
# view.py

     # IMPORT

    from django.shortcuts import render, redirect
    from django.contrib.auth import authenticate, login, logout
    from django.contrib.auth.decorators import login_required


    def connexion(request):
        username = request.POST.get('username', False)
        password = request.POST.get('password', False)

    next = request.POST.get('next', False)
    user = authenticate(username=username, password=password)
    
    if user is not None and user.is_active:
    
        print("user is login")
        """    
        login(request, user)

        if next: 
            return redirect(next)
        else:
            return redirect('homespe') # page si connect
        """
    else:
        return render(request, 'pages/login.html')  # page login
        
    @login_required(login_url='/connexion')
    
4 logout

     # view.py

    def deconnexion(request):
        logout(request)
        return redirect('mylogin') 
    
    
 ## user and authetification
 
 1 - Champs sur les users
 
  - __username__ : Champs obligatoires; 30 caractères ou moins. 
               Caractères alphanumériques uniquement (lettres, chiffres et traits de soulignement).
  - __first_name__ : Optionnel; 30 caractères ou moins.
  - __last_name__ : Optionnel; 30 caractères ou moins.
  - __email__ : Optionnel. Adresse e-mail.
  - __password__ : Champs obligatoires. Mot de passe et métadonnées sur le mot de passe (Django ne stocke
               pas le mot de passe brut). Voir la section «Mots de passe» pour plus d'informations 
               sur cette valeur.
  - __is_staff__ : Booléen Indique si cet utilisateur peut accéder au site d'administration.
  - __is_active__ : Booléen Indique si ce compte peut être utilisé pour se connecter. 
                Définissez cet indicateur sur au Falselieu de supprimer des comptes.
  - __is_superuser__ : Booléen Indique que cet utilisateur dispose de toutes les autorisations 
                   sans les attribuer explicitement.
  - __last_login__ : Une date-heure de la dernière connexion de l'utilisateur. 
                 Ceci est réglé sur la date / heure actuelle par défaut.
  - __date_joined__	 : Une date et heure indiquant la date de création du compte.
                   Ce paramètre est défini par défaut sur la date et l'heure actuelles lors de la création du compte.
                   
 2 - Méthodes
 
 - __is_authenticated()__ : Retourne toujours True pour les "vrais" Userobjets. C'est un moyen de savoir si 
                        l'utilisateur a été authentifié. Cela n'implique aucune autorisation et ne 
                        vérifie pas si l'utilisateur est actif. Cela indique seulement que l'utilisateur 
                        s'est authentifié avec succès.
 - __is_anonymous()__ : Renvoie True uniquement pour les AnonymousUserobjets (et False pour les «vrais» Userobjets). 
                    Généralement, vous devriez préférer utiliser is_authenticated() a cette méthode.
 - __get_full_name()__ : Renvoie le first_name plus le last_name, avec un espace entre les deux.
 - __set_password(passwd)__ : Définit le mot de passe de l'utilisateur sur la chaîne brute donnée, 
                        en prenant en charge le hachage du mot de passe. Cela ne sauve pas réellement l' Userobjet.
 - __check_password(passwd)__ : Retourne True si la chaîne brute donnée est le mot de passe correct pour l'utilisateur. 
                            Ceci prend en charge le hachage du mot de passe lors de la comparaison.
 - __get_group_permissions()__ : Renvoie une liste de chaînes de permission que l'utilisateur possède via les groupes 
                             auxquels il appartient.
  - __get_all_permissions()__ : Renvoie une liste des chaînes d'autorisation dont dispose l'utilisateur, à la fois par 
                            le biais d'autorisations de groupe et d'utilisateurs.
  - __has_perm(perm)__ : Retourne Truesi l'utilisateur a l'autorisation spécifiée, où permest dans le format "package.codename".                        Si l'utilisateur est inactif, cette méthode sera toujours renvoyée False.
  - __has_perms(perm_list)__ : Renvoie Truesi l'utilisateur possède toutes les autorisations spécifiées. 
                           Si l'utilisateur est inactif, cette méthode sera toujours renvoyée False.
  - __has_module_perms(app_label)__ : Retourne Truesi l'utilisateur a des permissions dans la donnée app_label. 
                                  Si l'utilisateur est inactif, cette méthode sera toujours renvoyée False.
  - __get_and_delete_messages()__ : Retourne une liste d' Messageobjets dans la file d'attente de l'utilisateur 
                                et supprime les messages de la file d'attente.
  - __email_user(subj, msg)__ : Envoie un courrier électronique à l'utilisateur. Cet email est envoyé depuis 
                            le DEFAULT_FROM_EMAIL paramètre. Vous pouvez également passer un troisième argument from_email,                                pour redéfinir l'adresse de l'expéditeur sur l'e-mail.
 
 3 - Verification de connexion et permission (les decorateurs)
        
   - login_required
   
         from django.contrib.auth.decorators import login_required
         
         @login_required
         def my_view(request):
              # ...
     - Verification de permissions
     
            def vote(request):
                if request.user.is_authenticated() and request.user.has_perm('polls.can_vote')):
                        # vote here
                else:
                        return HttpResponse("You can't vote in this poll.")
                        
     - user_passes_test
        
                def user_can_vote(user):
                        return user.is_authenticated() and user.has_perm("polls.can_vote")

                @user_passes_test(user_can_vote, login_url="/login/")
                def vote(request):
                        # Code here can assume a logged-in user with the correct permission.
                        ...
                  
     - permission_required()
        
            from django.contrib.auth.decorators import permission_required

            @permission_required('polls.can_vote', login_url="/login/")
            def vote(request):
                 # ...
       
       
   5 - create_user => Creation d'utilisateur django
   
        from django.contrib.auth.models import User
        
        user = User.objects.create_user(username='john', email='jlennon@beatles.com', password='glass onion')
        user.is_staff = True
        user.save()
       
   6 - changer le mot de passse
   
       user = User.objects.get(username='john')
       user.set_password('goo goo goo joob')
       user.save()
    
   7 - Creation dutilisateur avec form Django 
   
        # views.py
        from django import forms
        from django.contrib.auth.forms import UserCreationForm
        from django.http import HttpResponseRedirect
        from django.shortcuts import render_to_response

        def register(request):
            if request.method == 'POST':
                form = UserCreationForm(request.POST)
                if form.is_valid():
                    new_user = form.save()
                    return HttpResponseRedirect("/books/")
            else:
                form = UserCreationForm()
            return render_to_response("registration/register.html", {
                'form': form,
            }) 
            
            #template
            {% extends "base.html" %}

            {% block title %}Create an account{% endblock %}

             {% block content %}
              <h1>Create an account</h1>

              <form action="" method="post">
                     {{ form.as_p }}
                      <input type="submit" value="Create the account">
                </form>
               {% endblock %}
               
   8- Envoi de message a Utilisateur
   
        #views.py
        def create_playlist(request, songs):
            # Create the playlist with the given songs.
            # ...
            request.user.message_set.create(
                message="Your playlist was added successfully."
            )
            return render_to_response("playlists/create.html",
                context_instance=RequestContext(request))
         
        #create.html
        {% if messages %}
        <ul>
            {% for message in messages %}
            <li>{{ message }}</li>
            {% endfor %}
        </ul>
        {% endif %}
