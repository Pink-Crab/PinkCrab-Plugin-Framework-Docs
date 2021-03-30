---
description: >-
  Most of the BladeOne functions are used internally with the Blade wrappers,
  but we do allow calling them from the actual view object.
---

# BladeOne Functions

## Static Methods

* public static function e\($value\)
* public static function enq\($value\)
* public static function startsWith\($haystack, $needles\)
* public static function last\($array, callable $callback = null, $default = null\)
* public static function value\($value\)
* public static function first\($array, callable $callback = null, $default = null\)
* public static function contains\($haystack, $needles\)
* public static function get\($array, $key, $default = null\)
* public static function exists\($array, $key\)

```php
class Foo {
    protected $view;
    
    public function bar(){
        $escaped = $this->view::e("<p>Nope</p>Yes");
    }
}
```

## Instanced Methods

* public function showError\($id, $text, $critic = false\)
* public function format\($variable, $format = null\)
* public function wrapPHP\($input, $quote = '"', $parse = true\)
* public function isQuoted\($text\)
* public function addInclude\($view, $alias = null\)
* public function directive\($name, callable $handler\)
* public function stripParentheses\($expression\)
* public function setIsCompiled\($bool = false\)
* public function setPath\($templatePath, $compiledPath\)
* public function getAliasClasses\(\)
* public function setAliasClasses\($aliasClasses\)
* public function addAliasClasses\($aliasName, $classWithNS\)
* public function setAuth\($user = '', $role = null, $permission = \[\]\)
* public function runString\($string, $data = \[\]\)
* public function compileString\($value\)
* public function relative\($relativeWeb\)
* public function addAssetDict\($name, $url = ''\)
* public function compilePush\($expression\)
* public function compilePushOnce\($expression\)
* public function compilePrepend\($expression\)
* public function startPush\($section, $content = ''\)
* public function startPrepend\($section, $content = ''\)
* public function stopPush\(\)
* public function stopPrepend\(\)
* public function yieldPushContent\($section, $default = ''\)
* public function splitForeach\($each = 1, $splitText = ',', $splitEnd = ''\)
* public function registerIfStatement\($name, callable $callback\)
* public function check\($name, ...$parameters\)
* public function includeWhen\($bool = false, $view = '', $value = \[\]\)
* public function runChild\($view, $variables = \[\]\)
* public function compile\($templateName = null, $forced = false\)
* public function getCompiledFile\($templateName = ''\)
* public function getMode\(\)
* public function setMode\($mode\)
* public function getTemplateFile\($templateName = ''\)
* public function getFile\($fullFileName\)
* public function isExpired\($fileName\)
* public function includeFirst\($views = \[\], $value = \[\]\)
* public function convertArg\($array\)
* public function getCsrfToken\($fullToken = false, $tokenId = '\_token'\)
* public function regenerateToken\($tokenId = '\_token'\)
* public function ipClient\(\)
* public function csrfIsValid\($alwaysRegenerate = false, $tokenId = '\_token'\)
* public function yieldSection\(\)
* public function stopSection\($overwrite = false\)
* public function dump\($object, $jsconsole = false\)
* public function startSection\($section, $content = ''\)
* public function appendSection\(\)
* public function with\($varname, $value = null\)
* public function share\($varname, $value = null\)
* public function yieldContent\($section, $default = ''\)
* public function extend\(callable $compiler\)
* public function directiveRT\($name, callable $handler\)
* public function setEscapedContentTags\($openTag, $closeTag\)
* public function getContentTags\(\)
* public function setContentTags\($openTag, $closeTag, $escaped = false\)
* public function getEscapedContentTags\(\)
* public function setInjectResolver\(callable $function\)
* public function getFileExtension\(\)
* public function setFileExtension\($fileExtension\)
* public function getCompiledExtension\(\)
* public function setCompiledExtension\($fileExtension\)
* public function addLoop\($data\)
* public function incrementLoopIndices\(\)
* public function popLoop\(\)
* public function getFirstLoop\(\)
* public function renderEach\($view, $data, $iterator, $empty = 'raw\|'\)
* public function run\($view = null, $variables = \[\]\)
* public function setView\($view\)
* public function composer\($view = null, $functionOrClass = null\)
* public function startComponent\($name, array $data = \[\]\)
* public function renderComponent\(\)
* public function slot\($name, $content = null\)
* public function endSlot\(\)
* public function getPhpTag\(\)
* public function setPhpTag\($phpTag\)
* public function getCurrentUser\(\)
* public function setCurrentUser\($currentUser\)
* public function getCurrentRole\(\)
* public function setCurrentRole\($currentRole\)
* public function getCurrentPermission\(\)
* public function setCurrentPermission\($currentPermission\)
* public function getBaseUrl\(\)
* public function setBaseUrl\($baseUrl\)
* public function getCurrentUrlCalculated\($noArgs = false\)
* public function getRelativePath\(\)
* public function getCanonicalUrl\(\)
* public function setCanonicalUrl\($canonUrl = null\)
* public function getCurrentUrl\($noArgs = false\)
* public function setCurrentUrl\($currentUrl = null\)
* public function setOptimize\($bool = false\)
* public function setCanFunction\(callable $fn\)
* public function setAnyFunction\(callable $fn\)
* public function setErrorFunction\(callable $fn\)
* public function getLoopStack\(\)
* public function addInsideQuote\($quoted, $newFragment\)
* public function isVariablePHP\($text\)
* public function \_ef\($phrase\)
* public function \_e\($phrase\)
* public function \_n\($phrase, $phrases, $num = 0\)
* public function compileCanonical\($expression = null\)
* public function compileBase\($expression = null\)
* public function stripQuotes\($text\)
* public function parseArgs\($text, $separator = ','\)



```php
class Foo {
    protected $view;
    
    public function bar(){
        $this->view->isQuoted('"some data"'); // true
    }
}
```

