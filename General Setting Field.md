## Perfect! Here's a complete, practical example using Laravel + Filament v3 with Spatie Laravel Settings (the most popular and clean way).
## 1. Installation
Bash
```bash
    composer require spatie/laravel-settings
    composer require filament/spatie-laravel-settings-plugin:"^3.0" -W
    php artisan vendor:publish --provider="Spatie\LaravelSettings\LaravelSettingsServiceProvider" --tag="migrations"
    php artisan migrate
```
## 2. Create Settings Class (app/Settings/GeneralSettings.php)
PHP
```bash
    <?php
    namespace App\Settings;

    use Spatie\LaravelSettings\Settings;

    class GeneralSettings extends Settings
    {
        public string $app_name = 'My Application';
        public ?string $short_name = null;
        public ?string $tagline = null;

        // Colors (stored as hex)
        public string $primary_color = '#3b82f6';
        public string $secondary_color = '#64748b';
        public string $accent_color = '#22c55e';

        // Branding
        public ?string $logo = null;           // filename in storage/app/public/logos
        public ?string $favicon = null;
        public ?string $login_background = null;

        // Typography
        public string $primary_font = 'inter'; // inter, roboto, poppins, etc.

        // Layout
        public bool $dark_mode_enabled = true;
        public string $sidebar_style = 'fixed'; // fixed, collapsible

        public static function group(): string
        {
            return 'general';
        }
    }
```

## 3. Create Filament Settings Page
Bash
php artisan make:filament-settings-page ManageGeneralSettings GeneralSettings
This creates: app/Filament/Pages/ManageGeneralSettings.php
## 4. Full Settings Page Code
PHP
```bash
<?php

namespace App\Filament\Pages;

use App\Settings\GeneralSettings;
use Filament\Forms;
use Filament\Forms\Form;
use Filament\Pages\SettingsPage;

class ManageGeneralSettings extends SettingsPage
{
    protected static string $settings = GeneralSettings::class;

    protected static ?string $navigationIcon = 'heroicon-o-cog-8-tooth';
    protected static ?string $navigationGroup = 'Settings';
    protected static ?int $navigationSort = 1;
    protected static ?string $title = 'General Settings';
    protected static ?string $navigationLabel = 'General';

    public function form(Form $form): Form
    {
        return $form
            ->schema([
                Forms\Components\Section::make('Branding')
                    ->schema([
                        Forms\Components\TextInput::make('app_name')
                            ->label('Application Name')
                            ->required()
                            ->maxLength(255),

                        Forms\Components\TextInput::make('short_name')
                            ->label('Short Name'),

                        Forms\Components\TextInput::make('tagline')
                            ->label('Tagline'),

                        Forms\Components\FileUpload::make('logo')
                            ->label('Logo (Light)')
                            ->image()
                            ->directory('logos')
                            ->columnSpan(1),

                        Forms\Components\FileUpload::make('favicon')
                            ->label('Favicon')
                            ->image()
                            ->acceptedFileTypes(['image/x-icon', 'image/vnd.microsoft.icon', 'image/png'])
                            ->directory('logos'),
                    ])
                    ->columns(2),

                Forms\Components\Section::make('Colors & Theme')
                    ->schema([
                        Forms\Components\ColorPicker::make('primary_color')
                            ->label('Primary Color'),

                        Forms\Components\ColorPicker::make('secondary_color')
                            ->label('Secondary Color'),

                        Forms\Components\ColorPicker::make('accent_color')
                            ->label('Accent Color'),

                        Forms\Components\Toggle::make('dark_mode_enabled')
                            ->label('Enable Dark Mode'),
                    ])
                    ->columns(2),

                Forms\Components\Section::make('Typography')
                    ->schema([
                        Forms\Components\Select::make('primary_font')
                            ->options([
                                'inter' => 'Inter',
                                'roboto' => 'Roboto',
                                'poppins' => 'Poppins',
                                'opensans' => 'Open Sans',
                            ])
                            ->native(false),
                    ]),

                Forms\Components\Section::make('Layout')
                    ->schema([
                        Forms\Components\Select::make('sidebar_style')
                            ->options([
                                'fixed' => 'Fixed',
                                'collapsible' => 'Collapsible',
                            ]),
                    ]),
            ]);
    }
}
```
## 5. Register the Page (Optional but Recommended)
In app/Providers/Filament/AdminPanelProvider.php:
PHP
```bash
public function panel(Panel $panel): Panel
{
    return $panel
        ->pages([
            \App\Filament\Pages\ManageGeneralSettings::class,
            // ...
        ]);
}
```
## 6. How to Use These Settings in Your App
PHP
```bash
use App\Settings\GeneralSettings;

// In any controller, livewire, blade, etc.
$settings = app(GeneralSettings::class);

echo $settings->app_name;
echo $settings->primary_color;
```
For dynamic theming (CSS Variables):
Create a middleware or service provider that injects the colors:
blade
```bash
<style>
    :root {
        --primary-color: {{ app(GeneralSettings::class)->primary_color }};
    }
</style>
```
Then use in Tailwind: bg-[var(--primary-color)]
