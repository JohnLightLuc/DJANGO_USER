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
    
    
