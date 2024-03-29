# NAME

MooseX::Traits - automatically apply roles at object creation time

# VERSION

version 0.12

# SYNOPSIS

Given some roles:

    package Role;
    use Moose::Role;
    has foo => ( is => 'ro', isa => 'Int' required => 1 );

And a class:

    package Class;
    use Moose;
    with 'MooseX::Traits';

Apply the roles to the class at `new` time:

    my $class = Class->with_traits('Role')->new( foo => 42 );

Then use your customized class:

    $class->isa('Class'); # true
    $class->does('Role'); # true
    $class->foo; # 42

# DESCRIPTION

Often you want to create components that can be added to a class
arbitrarily.  This module makes it easy for the end user to use these
components.  Instead of requiring the user to create a named class
with the desired roles applied, or apply roles to the instance
one-by-one, he can just create a new class from yours with
`with_traits`, and then instantiate that.

There is also `new_with_traits`, which exists for compatibility
reasons.  It accepts a `traits` parameter, creates a new class with
those traits, and then instantiates it.

    Class->new_with_traits( traits => [qw/Foo Bar/], foo => 42, bar => 1 )

returns exactly the same object as

    Class->with_traits(qw/Foo Bar/)->new( foo => 42, bar => 1 )

would.  But you can also store the result of `with_traits`, and call
other methods:

    my $c = Class->with_traits(qw/Foo Bar/);
    $c->new( foo => 42 );
    $c->whatever( foo => 1234 );

And so on.

# METHODS

- __$class->with\_traits( @traits )__

    Return a new class with the traits applied.  Use like:

- __$class->new\_with\_traits(%args, traits => \\@traits)__

    `new_with_traits` can also take a hashref, e.g.:

        my $instance = $class->new_with_traits({ traits => \@traits, foo => 'bar' });

# ATTRIBUTES YOUR CLASS GETS

This role will add the following attributes to the consuming class.

## \_trait\_namespace

You can override the value of this attribute with `default` to
automatically prepend a namespace to the supplied traits.  (This can
be overridden by prefixing the trait name with `+`.)

Example:

    package Another::Trait;
    use Moose::Role;
    has 'bar' => (
        is       => 'ro',
        isa      => 'Str',
        required => 1,
    );

    package Another::Class;
    use Moose;
    with 'MooseX::Traits';
    has '+_trait_namespace' => ( default => 'Another' );

    my $instance = Another::Class->new_with_traits(
        traits => ['Trait'], # "Another::Trait", not "Trait"
        bar    => 'bar',
    );
    $instance->does('Trait')          # false
    $instance->does('Another::Trait') # true

    my $instance2 = Another::Class->new_with_traits(
        traits => ['+Trait'], # "Trait", not "Another::Trait"
    );
    $instance2->does('Trait')          # true
    $instance2->does('Another::Trait') # false

# AUTHOR

Jonathan Rockway <jrockway@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2008 by Infinity Interactive, Inc. http://www.iinteractive.com.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.

# CONTRIBUTORS

- Chris Prather <chris@prather.org>
- Florian Ragwitz <rafl@debian.org>
- Hans Dieter Pearcey <hdp@weftsoar.net>
- Jesse Luehrs <doy@tozt.net>
- Karen Etheridge <ether@cpan.org>
- Matt S. Trout <mst@shadowcatsystems.co.uk>
- Rafael Kitover <rkitover@cpan.org>
- Shawn Moore <sartak@bestpractical.com>
- Stevan Little <stevan.little@iinteractive.com>
- Tomas Doran <bobtfish@bobtfish.net>
- Yuval Kogman <nothingmuch@woobling.org>
