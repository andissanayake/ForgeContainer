This is the source code for [asp.net][1].

`AddDefaultIdentity` implementation


    public static IdentityBuilder AddDefaultIdentity<TUser>(this IServiceCollection services, Action<IdentityOptions> configureOptions) where TUser : class
    {
        services.AddAuthentication(o =>
        {
            o.DefaultScheme = IdentityConstants.ApplicationScheme;
            o.DefaultSignInScheme = IdentityConstants.ExternalScheme;
        })
        .AddIdentityCookies(o => { });
     
        return services.AddIdentityCore<TUser>(o =>
        {
            o.Stores.MaxLengthForKeys = 128;
            configureOptions?.Invoke(o);
        })
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    }

`AddIdentityApiEndpoints` implementation


    public static IdentityBuilder AddIdentityApiEndpoints<TUser>(this IServiceCollection services, Action<IdentityOptions> configure)
        where TUser : class, new()
    {
        ArgumentNullException.ThrowIfNull(services);
        ArgumentNullException.ThrowIfNull(configure);
     
        services
            .AddAuthentication(IdentityConstants.BearerAndApplicationScheme)
            .AddScheme<AuthenticationSchemeOptions, CompositeIdentityHandler>(IdentityConstants.BearerAndApplicationScheme, null, compositeOptions =>
            {
                compositeOptions.ForwardDefault = IdentityConstants.BearerScheme;
                compositeOptions.ForwardAuthenticate = IdentityConstants.BearerAndApplicationScheme;
            })
            .AddBearerToken(IdentityConstants.BearerScheme)
            .AddIdentityCookies();
     
        return services.AddIdentityCore<TUser>(configure)
            .AddApiEndpoints();
    }

`AddIdentityCore` implementation

    public static IdentityBuilder AddIdentityCore<TUser>(this IServiceCollection services, Action<IdentityOptions> setupAction)
            where TUser : class
        {
            // Services identity depends on
            services.AddOptions().AddLogging();
     
            // Services used by identity
            services.TryAddScoped<IUserValidator<TUser>, UserValidator<TUser>>();
            services.TryAddScoped<IPasswordValidator<TUser>, PasswordValidator<TUser>>();
            services.TryAddScoped<IPasswordHasher<TUser>, PasswordHasher<TUser>>();
            services.TryAddScoped<ILookupNormalizer, UpperInvariantLookupNormalizer>();
            services.TryAddScoped<IUserConfirmation<TUser>, DefaultUserConfirmation<TUser>>();
            // No interface for the error describer so we can add errors without rev'ing the interface
            services.TryAddScoped<IdentityErrorDescriber>();
            services.TryAddScoped<IUserClaimsPrincipalFactory<TUser>, UserClaimsPrincipalFactory<TUser>>();
            services.TryAddScoped<UserManager<TUser>>();
     
            if (setupAction != null)
            {
                services.Configure(setupAction);
            }
     
            return new IdentityBuilder(typeof(TUser), services);
        }
    	





  [1]: https://source.dot.net/




