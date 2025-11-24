# Spring PetClinic - Modern UI Design Guide

## Design System

### Color Palette
```
Primary Gradient:
  - Start: #667eea (Modern Purple-Blue)
  - End:   #764ba2 (Deep Purple)

Backgrounds:
  - Page BG: #f8fafb (Light Gray)
  - Card BG: #ffffff (White)

Text Colors:
  - Primary Text: #2c3e50 (Professional Dark Blue)
  - Secondary Text: #666666 (Medium Gray)
  - Muted Text: #999999 (Light Gray)
  - White Text: #ffffff (For dark backgrounds)
```

### Typography
```
Font Stack: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif

Headings:
  H1: 32px, weight 700, line-height 40px, letter-spacing -0.5px
  H2: 24px, weight 700, line-height 32px
  H3: 18px, weight 600, line-height 24px
  
Body: 14px, weight 400, line-height 1.5
```

### Spacing Scale
```
xs:  4px
sm:  8px
md:  12px
lg:  16px
xl:  20px
2xl: 24px
3xl: 32px
4xl: 40px
```

### Border Radius
```
Small:  4px (input focus, small elements)
Medium: 6px (buttons, inputs, cards)
Large:  8px (cards, modals)
Full:   50rem (pill buttons, badges)
```

### Shadows
```
Shadow XS: 0 2px 8px rgba(0, 0, 0, 0.1)
Shadow SM: 0 4px 12px rgba(0, 0, 0, 0.07)
Shadow MD: 0 4px 6px rgba(0, 0, 0, 0.07)
Shadow LG: 0 12px 24px rgba(0, 0, 0, 0.15)
```

### Transitions
```
Easing: cubic-bezier(0.4, 0, 0.2, 1) (Material Design standard)
Duration: 300ms (for most interactions)
```

## Component Styles

### Buttons
```css
.btn-primary {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 6px;
  padding: 0.75rem 1.5rem;
  font-weight: 500;
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.btn-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(102, 126, 234, 0.5);
}
```

### Form Inputs
```css
.form-control {
  border: 1.5px solid #e0e0e0;
  border-radius: 6px;
  padding: 0.75rem;
  transition: all 0.3s ease;
}

.form-control:focus {
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
  outline: none;
}
```

### Cards
```css
.card {
  border: none;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.07);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.card:hover {
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.15);
  transform: translateY(-4px);
}

.card-header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 8px 8px 0 0;
}
```

### Tables
```css
.table > thead > tr > th {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  font-weight: 600;
  padding: 1rem;
  border: none;
}

.table > tbody > tr:hover {
  background-color: #f8f9ff;
}
```

### Navigation Bar
```css
.navbar {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-bottom: 2px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.navbar li > a {
  color: rgba(255, 255, 255, 0.95);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.navbar li:hover > a {
  color: white;
  background-color: rgba(255, 255, 255, 0.1);
  border-radius: 4px;
  transform: translateY(-2px);
}
```

## Animation Patterns

### Hover Elevation
Elements lift up slightly on hover with increased shadow:
```css
transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);

&:hover {
  transform: translateY(-2px);
  box-shadow: /* larger shadow */;
}
```

### Focus State
Form elements show a subtle colored glow on focus:
```css
.form-control:focus {
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}
```

### Smooth Transitions
All interactive elements use consistent easing for professional feel.

## Accessibility Features

- Color contrast ratios meet WCAG AA standards
- Focus states clearly visible for keyboard navigation
- Semantic HTML structure preserved
- Descriptive alt text for images
- Proper heading hierarchy
- Sufficient target sizes for touch interactions

## Usage Examples

### Creating a Modern Card
```html
<div class="card">
  <div class="card-header">
    <h4 class="card-title">Title</h4>
  </div>
  <div class="card-body">
    <p>Content here</p>
  </div>
</div>
```

### Creating a Modern Button
```html
<button class="btn btn-primary">Click Me</button>
```

### Creating a Modern Form
```html
<div class="form-group">
  <label for="email">Email</label>
  <input type="email" class="form-control" id="email" placeholder="Enter email">
  <small class="help-block">We'll never share your email.</small>
</div>
```

## Responsive Design
- Mobile-first approach maintained
- Breakpoints: 576px (sm), 768px (md), 992px (lg), 1200px (xl), 1400px (xxl)
- Flexible grid system preserved
- All animations smooth on mobile

## Migration Notes
- No HTML changes required
- Existing Bootstrap classes still work
- Custom CSS only modifications
- Backward compatible with existing markup
- Can be further customized with new CSS variables
