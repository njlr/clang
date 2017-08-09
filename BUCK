from os.path import basename
from os.path import dirname
from hashlib import sha256

prebuilt_cxx_library(
  name = 'cmake-generated',
  header_only = True,
  header_namespace = '',
  exported_platform_headers = [
    (
      'default',
      subdir_glob([
        ('cmake-generated/macosx-x86_64/include', 'clang/**/*.h'),
        ('cmake-generated/macosx-x86_64/include', 'clang/**/*.inc'),
      ])
    ),
    (
      '^macos.*',
      subdir_glob([
        ('cmake-generated/macosx-x86_64/include', 'clang/**/*.h'),
        ('cmake-generated/macosx-x86_64/include', 'clang/**/*.inc'),
      ])
    ),
  ],
)

cxx_binary(
  name = 'clang-tblgen',
  srcs = glob([
    'utils/TableGen/**/*.cpp',
  ]),
  linker_flags = [
    '-lncurses',
  ],
  deps = [
    'llvm//:llvm',
  ],
  visibility = [
    'PUBLIC',
  ],
)

def llvm_tablegen(x, n, t):
  digest = sha256(x + n + t).hexdigest()[0:12]
  parent = dirname(x)
  file_name = basename(n)
  genrule(
    name = 'llvm-tblgen-' + n + '-' + digest,
    out = file_name,
    srcs = glob([
      'include/llvm/**/*.td',
      parent + '/**/*.td',
    ]),
    cmd = '$(location llvm//:llvm-tblgen) ' + t + ' -I $SRCDIR/include -I $SRCDIR/' + parent + ' $SRCDIR/' + x + ' -o $OUT',
  )
  prebuilt_cxx_library(
    name = n + '-' + digest,
    header_namespace = '',
    header_only = True,
    exported_headers = {
      n: ':' + 'llvm-tblgen-' + n + '-' + digest,
    },
  )
  return ':' + n + '-' + digest

def tablegen(x, n, t):
  digest = sha256(x + n + t).hexdigest()[0:12]
  parent = dirname(x)
  file_name = basename(n)
  genrule(
    name = 'clang-tblgen-' + n + '-' + digest,
    out = file_name,
    srcs = glob([
      'include/llvm/**/*.td',
      parent + '/**/*.td',
    ]),
    cmd = '$(location :clang-tblgen) ' + t + ' -I $SRCDIR/include -I $SRCDIR/' + parent + ' $SRCDIR/' + x + ' -o $OUT',
  )
  prebuilt_cxx_library(
    name = n + '-' + digest,
    header_namespace = '',
    header_only = True,
    exported_headers = {
      n: ':' + 'clang-tblgen-' + n + '-' + digest,
    },
  )
  return ':' + n + '-' + digest

cxx_library(
  name = 'clang',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('include', 'clang/**/*.h'),
    ('include', 'clang/**/*.def'),
    ('include', 'clang-c/**/*.h'),
  ]),
  headers = subdir_glob([
    ('lib/Driver', '**/*.h'),
  ]),
  srcs = glob([
    'lib/**/*.cpp',
  ]),
  deps = [
    ':cmake-generated',
    'llvm//:llvm',
    tablegen('include/clang/AST/CommentHTMLTags.td', 'clang/AST/CommentHTMLTags.inc', '-gen-clang-comment-html-tags'),
    tablegen('include/clang/AST/CommentHTMLTags.td', 'clang/AST/CommentHTMLTagsProperties.inc', '-gen-clang-comment-html-tags-properties'),
    tablegen('include/clang/AST/CommentHTMLNamedCharacterReferences.td', 'clang/AST/CommentHTMLNamedCharacterReferences.inc', '-gen-clang-comment-html-named-character-references'),
    tablegen('include/clang/AST/CommentCommands.td', 'clang/AST/CommentCommandInfo.inc', '-gen-clang-comment-command-info'),
    tablegen('include/clang/AST/CommentCommands.td', 'clang/AST/CommentCommandList.inc', '-gen-clang-comment-command-list'),
    tablegen('include/clang/Basic/DeclNodes.td', 'clang/AST/DeclNodes.inc', '-gen-clang-decl-nodes'),
    tablegen('include/clang/Basic/CommentNodes.td', 'clang/AST/CommentNodes.inc', '-gen-clang-comment-nodes'),
    tablegen('include/clang/Basic/StmtNodes.td', 'clang/AST/StmtNodes.inc', '-gen-clang-stmt-nodes'),
    tablegen('include/clang/Basic/arm_neon.td', 'clang/Basic/arm_neon.inc', '-gen-arm-neon-sema'),
    tablegen('include/clang/Basic/Attr.td', 'clang/AST/Attrs.inc', '-gen-clang-attr-classes'),
    tablegen('include/clang/Basic/Attr.td', 'clang/AST/AttrDump.inc', '-gen-clang-attr-dump'),
    tablegen('include/clang/Basic/Attr.td', 'clang/AST/AttrVisitor.inc', '-gen-clang-attr-ast-visitor'),
    tablegen('include/clang/Basic/Attr.td', 'clang/AST/AttrImpl.inc', '-gen-clang-attr-impl'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Basic/AttrList.inc', '-gen-clang-attr-list'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Basic/AttrSubMatchRulesList.inc', '-gen-clang-attr-subject-match-rule-list'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Basic/AttrHasAttributeImpl.inc', '-gen-clang-attr-has-attribute-impl'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Parse/AttrParserStringSwitches.inc', '-gen-clang-attr-parser-string-switches'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Parse/AttrSubMatchRulesParserStringSwitches.inc', '-gen-clang-attr-subject-match-rules-parser-string-switches'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Sema/AttrParsedAttrList.inc', '-gen-clang-attr-parsed-attr-list'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Sema/AttrParsedAttrKinds.inc', '-gen-clang-attr-parsed-attr-kinds'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Sema/AttrTemplateInstantiate.inc', '-gen-clang-attr-template-instantiate'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Sema/AttrSpellingListIndex.inc', '-gen-clang-attr-spelling-index'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Sema/AttrParsedAttrImpl.inc', '-gen-clang-attr-parsed-attr-impl'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Serialization/AttrPCHRead.inc', '-gen-clang-attr-pch-read'),
    tablegen('include/clang/Basic/Attr.td', 'clang/Serialization/AttrPCHWrite.inc', '-gen-clang-attr-pch-write'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticAnalysisKinds.inc', '-gen-clang-diags-defs -clang-component=Analysis'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticASTKinds.inc', '-gen-clang-diags-defs -clang-component=AST'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticCommentKinds.inc', '-gen-clang-diags-defs -clang-component=Comment'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticCommonKinds.inc', '-gen-clang-diags-defs -clang-component=Common'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticDriverKinds.inc', '-gen-clang-diags-defs -clang-component=Driver'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticFrontendKinds.inc', '-gen-clang-diags-defs -clang-component=Frontend'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticGroups.inc', '-gen-clang-diag-groups'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticLexKinds.inc', '-gen-clang-diags-defs -clang-component=Lex'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticParseKinds.inc', '-gen-clang-diags-defs -clang-component=Parse'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticSerializationKinds.inc', '-gen-clang-diags-defs -clang-component=Serialization'),
    tablegen('include/clang/Basic/Diagnostic.td', 'clang/Basic/DiagnosticSemaKinds.inc', '-gen-clang-diags-defs -clang-component=Sema'),
    tablegen('include/clang/StaticAnalyzer/Checkers/Checkers.td', 'clang/StaticAnalyzer/Checkers/Checkers.inc', '-gen-clang-sa-checkers'),
    llvm_tablegen('include/clang/Driver/Options.td', 'clang/Driver/Options.inc', '-gen-opt-parser-defs'),
  ],
  visibility = [
    'PUBLIC',
  ],
)
