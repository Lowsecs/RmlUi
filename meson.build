project(
  'RmlUi',
  'cpp',
  version: '6.2',
  license: 'MIT',
  meson_version: '>=0.55.0',
  default_options: ['cpp_std=c++14', 'default_library=static'],
)

# Options
font_engine = get_option('font_engine')
harfbuzz_text_shaping = get_option('harfbuzz_text_shaping')
lua_bindings = get_option('lua_bindings')
lottie_plugin = get_option('lottie_plugin')
svg_plugin = get_option('svg_plugin')
thirdparty_containers = get_option('thirdparty_containers')
matrix_row_major = get_option('matrix_row_major')
rmlui_custom_rtti_impl = get_option('rmlui_custom_rtti_impl')
precompiled_headers = get_option('precompiled_headers')

# Dependencies
freetype_dep = dependency('freetype2', required: font_engine == 'freetype')
harfbuzz_dep = dependency('harfbuzz', required: harfbuzz_text_shaping)

# Handle optional dependencies gracefully.
# If the plugin is enabled but the dependency is not found, Meson will disable the option.
rlottie_dep = dependency('rlottie', required: get_option('lottie_plugin'))
if not rlottie_dep.found() and get_option('lottie_plugin')
  message('rlottie dependency not found, disabling lottie_plugin.')
  lottie_plugin = false
endif
lunasvg_dep = dependency('lunasvg', required: get_option('svg_plugin'))
if not lunasvg_dep.found() and get_option('svg_plugin')
  message('lunasvg dependency not found, disabling svg_plugin.')
  svg_plugin = false
endif

# Include directories
inc_dirs = include_directories('Include')

# Compile definitions
compile_args = []
if get_option('default_library') == 'static'
  compile_args += '-DRMLUI_STATIC_LIB'
else
  compile_args += '-DRMLUI_CORE_EXPORTS'
endif

# WORKAROUND: Handle builds with exceptions disabled.
# The third-party container library 'itlib::flat_map' uses 'throw' by default.
# Defining 'ITLIB_FLAT_MAP_NO_THROW' replaces 'throw' with 'assert', which is
# compatible with -fno-exceptions.
if get_option('cpp_eh') == 'none'
  compile_args += '-DITLIB_FLAT_MAP_NO_THROW'
endif

if not thirdparty_containers
  compile_args += '-DRMLUI_NO_THIRDPARTY_CONTAINERS'
endif

# WORKAROUND: Handle builds with RTTI disabled.
# RmlUi uses 'typeid' which requires RTTI. Enabling this option compiles in
# a custom RTTI implementation and disables the use of 'typeid'.
if rmlui_custom_rtti_impl
  compile_args += '-DRMLUI_CUSTOM_RTTI'
endif
if matrix_row_major
  compile_args += '-DRMLUI_MATRIX_ROW_MAJOR'
endif

# Precompiled headers
if precompiled_headers
  pch_file = 'Source/Core/precompiled.h'
else
  pch_file = ''
endif

# Source files
rmlui_core_sources = [
  'Source/Core/BaseXMLParser.cpp',
  'Source/Core/Box.cpp',
  'Source/Core/BoxShadowCache.cpp',
  'Source/Core/CallbackTexture.cpp',
  'Source/Core/Clock.cpp',
  'Source/Core/CompiledFilterShader.cpp',
  'Source/Core/ComputedValues.cpp',
  'Source/Core/ComputeProperty.cpp',
  'Source/Core/Context.cpp',
  'Source/Core/ContextInstancer.cpp',
  'Source/Core/ContextInstancerDefault.cpp',
  'Source/Core/ConvolutionFilter.cpp',
  'Source/Core/Core.cpp',
  'Source/Core/DataController.cpp',
  'Source/Core/DataControllerDefault.cpp',
  'Source/Core/DataExpression.cpp',
  'Source/Core/DataModel.cpp',
  'Source/Core/DataModelHandle.cpp',
  'Source/Core/DataTypeRegister.cpp',
  'Source/Core/DataVariable.cpp',
  'Source/Core/DataView.cpp',
  'Source/Core/DataViewDefault.cpp',
  'Source/Core/Decorator.cpp',
  'Source/Core/DecoratorGradient.cpp',
  'Source/Core/DecoratorNinePatch.cpp',
  'Source/Core/DecoratorShader.cpp',
  'Source/Core/DecoratorText.cpp',
  'Source/Core/DecoratorTiled.cpp',
  'Source/Core/DecoratorTiledBox.cpp',
  'Source/Core/DecoratorTiledHorizontal.cpp',
  'Source/Core/DecoratorTiledImage.cpp',
  'Source/Core/DecoratorTiledVertical.cpp',
  'Source/Core/DecoratorUtilities.cpp',
  'Source/Core/DocumentHeader.cpp',
  'Source/Core/EffectSpecification.cpp',
  'Source/Core/Element.cpp',
  'Source/Core/ElementAnimation.cpp',
  'Source/Core/ElementBackgroundBorder.cpp',
  'Source/Core/ElementDefinition.cpp',
  'Source/Core/ElementDocument.cpp',
  'Source/Core/ElementEffects.cpp',
  'Source/Core/ElementHandle.cpp',
  'Source/Core/ElementInstancer.cpp',
  'Source/Core/ElementMeta.cpp',
  'Source/Core/ElementScroll.cpp',
  'Source/Core/ElementStyle.cpp',
  'Source/Core/ElementText.cpp',
  'Source/Core/ElementUtilities.cpp',
  'Source/Core/Event.cpp',
  'Source/Core/EventDispatcher.cpp',
  'Source/Core/EventInstancer.cpp',
  'Source/Core/EventInstancerDefault.cpp',
  'Source/Core/EventListenerInstancer.cpp',
  'Source/Core/EventSpecification.cpp',
  'Source/Core/Factory.cpp',
  'Source/Core/FileInterface.cpp',
  'Source/Core/FileInterfaceDefault.cpp',
  'Source/Core/Filter.cpp',
  'Source/Core/FilterBasic.cpp',
  'Source/Core/FilterBlur.cpp',
  'Source/Core/FilterDropShadow.cpp',
  'Source/Core/FontEffect.cpp',
  'Source/Core/FontEffectBlur.cpp',
  'Source/Core/FontEffectGlow.cpp',
  'Source/Core/FontEffectInstancer.cpp',
  'Source/Core/FontEffectOutline.cpp',
  'Source/Core/FontEffectShadow.cpp',
  'Source/Core/FontEngineInterface.cpp',
  'Source/Core/Geometry.cpp',
  'Source/Core/GeometryBackgroundBorder.cpp',
  'Source/Core/GeometryBoxShadow.cpp',
  'Source/Core/Log.cpp',
  'Source/Core/LogDefault.cpp',
  'Source/Core/Math.cpp',
  'Source/Core/Memory.cpp',
  'Source/Core/MeshUtilities.cpp',
  'Source/Core/ObserverPtr.cpp',
  'Source/Core/Plugin.cpp',
  'Source/Core/PluginRegistry.cpp',
  'Source/Core/Profiling.cpp',
  'Source/Core/PropertiesIteratorView.cpp',
  'Source/Core/Property.cpp',
  'Source/Core/PropertyDefinition.cpp',
  'Source/Core/PropertyDictionary.cpp',
  'Source/Core/PropertyParserAnimation.cpp',
  'Source/Core/PropertyParserBoxShadow.cpp',
  'Source/Core/PropertyParserColorStopList.cpp',
  'Source/Core/PropertyParserColour.cpp',
  'Source/Core/PropertyParserDecorator.cpp',
  'Source/Core/PropertyParserFilter.cpp',
  'Source/Core/PropertyParserFontEffect.cpp',
  'Source/Core/PropertyParserKeyword.cpp',
  'Source/Core/PropertyParserNumber.cpp',
  'Source/Core/PropertyParserRatio.cpp',
  'Source/Core/PropertyParserString.cpp',
  'Source/Core/PropertyParserTransform.cpp',
  'Source/Core/PropertySpecification.cpp',
  'Source/Core/RenderInterface.cpp',
  'Source/Core/RenderInterfaceCompatibility.cpp',
  'Source/Core/RenderManager.cpp',
  'Source/Core/RenderManagerAccess.cpp',
  'Source/Core/ScrollController.cpp',
  'Source/Core/Spritesheet.cpp',
  'Source/Core/Stream.cpp',
  'Source/Core/StreamFile.cpp',
  'Source/Core/StreamMemory.cpp',
  'Source/Core/StringUtilities.cpp',
  'Source/Core/StyleSheet.cpp',
  'Source/Core/StyleSheetContainer.cpp',
  'Source/Core/StyleSheetFactory.cpp',
  'Source/Core/StyleSheetNode.cpp',
  'Source/Core/StyleSheetParser.cpp',
  'Source/Core/StyleSheetSelector.cpp',
  'Source/Core/StyleSheetSpecification.cpp',
  'Source/Core/SystemInterface.cpp',
  'Source/Core/Template.cpp',
  'Source/Core/TemplateCache.cpp',
  'Source/Core/Texture.cpp',
  'Source/Core/TextureDatabase.cpp',
  'Source/Core/TextureLayout.cpp',
  'Source/Core/TextureLayoutRectangle.cpp',
  'Source/Core/TextureLayoutRow.cpp',
  'Source/Core/TextureLayoutTexture.cpp',
  'Source/Core/Traits.cpp',
  'Source/Core/Transform.cpp',
  'Source/Core/TransformPrimitive.cpp',
  'Source/Core/TransformState.cpp',
  'Source/Core/TransformUtilities.cpp',
  'Source/Core/Tween.cpp',
  'Source/Core/TypeConverter.cpp',
  'Source/Core/URL.cpp',
  'Source/Core/Variant.cpp',
  'Source/Core/WidgetScroll.cpp',
  'Source/Core/XMLNodeHandler.cpp',
  'Source/Core/XMLNodeHandlerBody.cpp',
  'Source/Core/XMLNodeHandlerDefault.cpp',
  'Source/Core/XMLNodeHandlerHead.cpp',
  'Source/Core/XMLNodeHandlerTemplate.cpp',
  'Source/Core/XMLParser.cpp',
  'Source/Core/XMLParseTools.cpp',
  'Source/Core/Elements/ElementFormControl.cpp',
  'Source/Core/Elements/ElementFormControlInput.cpp',
  'Source/Core/Elements/ElementFormControlSelect.cpp',
  'Source/Core/Elements/ElementFormControlTextArea.cpp',
  'Source/Core/Elements/ElementForm.cpp',
  'Source/Core/Elements/ElementImage.cpp',
  'Source/Core/Elements/ElementLabel.cpp',
  'Source/Core/Elements/ElementProgress.cpp',
  'Source/Core/Elements/ElementTabSet.cpp',
  'Source/Core/Elements/ElementTextSelection.cpp',
  'Source/Core/Elements/InputTypeButton.cpp',
  'Source/Core/Elements/InputTypeCheckbox.cpp',
  'Source/Core/Elements/InputType.cpp',
  'Source/Core/Elements/InputTypeRadio.cpp',
  'Source/Core/Elements/InputTypeRange.cpp',
  'Source/Core/Elements/InputTypeSubmit.cpp',
  'Source/Core/Elements/InputTypeText.cpp',
  'Source/Core/Elements/WidgetDropDown.cpp',
  'Source/Core/Elements/WidgetSlider.cpp',
  'Source/Core/Elements/WidgetTextInput.cpp',
  'Source/Core/Elements/WidgetTextInputMultiLine.cpp',
  'Source/Core/Elements/WidgetTextInputSingleLine.cpp',
  'Source/Core/Elements/WidgetTextInputSingleLinePassword.cpp',
  'Source/Core/Elements/XMLNodeHandlerSelect.cpp',
  'Source/Core/Elements/XMLNodeHandlerTabSet.cpp',
  'Source/Core/Elements/XMLNodeHandlerTextArea.cpp',
  'Source/Core/Layout/BlockContainer.cpp',
  'Source/Core/Layout/BlockFormattingContext.cpp',
  'Source/Core/Layout/ContainerBox.cpp',
  'Source/Core/Layout/FlexFormattingContext.cpp',
  'Source/Core/Layout/FloatedBoxSpace.cpp',
  'Source/Core/Layout/FormattingContext.cpp',
  'Source/Core/Layout/InlineBox.cpp',
  'Source/Core/Layout/InlineContainer.cpp',
  'Source/Core/Layout/InlineLevelBox.cpp',
  'Source/Core/Layout/LayoutBox.cpp',
  'Source/Core/Layout/LayoutDetails.cpp',
  'Source/Core/Layout/LayoutEngine.cpp',
  'Source/Core/Layout/LayoutPools.cpp',
  'Source/Core/Layout/LineBox.cpp',
  'Source/Core/Layout/ReplacedFormattingContext.cpp',
  'Source/Core/Layout/TableFormattingContext.cpp',
  'Source/Core/Layout/TableFormattingDetails.cpp',
]

if font_engine == 'freetype'
  rmlui_core_sources += [
    'Source/Core/FontEngineDefault/FontEngineInterfaceDefault.cpp',
    'Source/Core/FontEngineDefault/FontFace.cpp',
    'Source/Core/FontEngineDefault/FontFaceHandleDefault.cpp',
    'Source/Core/FontEngineDefault/FontFaceLayer.cpp',
    'Source/Core/FontEngineDefault/FontFamily.cpp',
    'Source/Core/FontEngineDefault/FontProvider.cpp',
    'Source/Core/FontEngineDefault/FreeTypeInterface.cpp',
  ]
endif

# Library
rmlui_core = library(
  'rmlui_core',
  sources: rmlui_core_sources,
  include_directories: inc_dirs,
  dependencies: [freetype_dep, harfbuzz_dep, rlottie_dep, lunasvg_dep],
  cpp_args: compile_args,
  cpp_pch: pch_file,
  install: true,
)

install_headers(
  'Include/RmlUi/Core.h',
  'Include/RmlUi/Config/Config.h',
  'Include/RmlUi/Core/Animation.h',
  'Include/RmlUi/Core/BaseXMLParser.h',
  'Include/RmlUi/Core/Box.h',
  'Include/RmlUi/Core/CallbackTexture.h',
  'Include/RmlUi/Core/Colour.h',
  'Include/RmlUi/Core/CompiledFilterShader.h',
  'Include/RmlUi/Core/ComputedValues.h',
  'Include/RmlUi/Core/Context.h',
  'Include/RmlUi/Core/ContextInstancer.h',
  'Include/RmlUi/Core/ConvolutionFilter.h',
  'Include/RmlUi/Core/Core.h',
  'Include/RmlUi/Core/DataModelHandle.h',
  'Include/RmlUi/Core/DataStructHandle.h',
  'Include/RmlUi/Core/DataTypeRegister.h',
  'Include/RmlUi/Core/DataTypes.h',
  'Include/RmlUi/Core/DataVariable.h',
  'Include/RmlUi/Core/Debug.h',
  'Include/RmlUi/Core/DecorationTypes.h',
  'Include/RmlUi/Core/Decorator.h',
  'Include/RmlUi/Core/Dictionary.h',
  'Include/RmlUi/Core/EffectSpecification.h',
  'Include/RmlUi/Core/Element.h',
  'Include/RmlUi/Core/ElementDocument.h',
  'Include/RmlUi/Core/ElementInstancer.h',
  'Include/RmlUi/Core/ElementScroll.h',
  'Include/RmlUi/Core/ElementText.h',
  'Include/RmlUi/Core/ElementUtilities.h',
  'Include/RmlUi/Core/Elements/ElementForm.h',
  'Include/RmlUi/Core/Elements/ElementFormControl.h',
  'Include/RmlUi/Core/Elements/ElementFormControlInput.h',
  'Include/RmlUi/Core/Elements/ElementFormControlSelect.h',
  'Include/RmlUi/Core/Elements/ElementFormControlTextArea.h',
  'Include/RmlUi/Core/Elements/ElementProgress.h',
  'Include/RmlUi/Core/Elements/ElementTabSet.h',
  'Include/RmlUi/Core/Event.h',
  'Include/RmlUi/Core/EventInstancer.h',
  'Include/RmlUi/Core/EventListener.h',
  'Include/RmlUi/Core/EventListenerInstancer.h',
  'Include/RmlUi/Core/Factory.h',
  'Include/RmlUi/Core/FileInterface.h',
  'Include/RmlUi/Core/Filter.h',
  'Include/RmlUi/Core/FontEffect.h',
  'Include/RmlUi/Core/FontEffectInstancer.h',
  'Include/RmlUi/Core/FontEngineInterface.h',
  'Include/RmlUi/Core/FontGlyph.h',
  'Include/RmlUi/Core/FontMetrics.h',
  'Include/RmlUi/Core/Geometry.h',
  'Include/RmlUi/Core/Header.h',
  'Include/RmlUi/Core/ID.h',
  'Include/RmlUi/Core/Input.h',
  'Include/RmlUi/Core/Log.h',
  'Include/RmlUi/Core/Math.h',
  'Include/RmlUi/Core/Matrix4.h',
  'Include/RmlUi/Core/Mesh.h',
  'Include/RmlUi/Core/MeshUtilities.h',
  'Include/RmlUi/Core/NumericValue.h',
  'Include/RmlUi/Core/ObserverPtr.h',
  'Include/RmlUi/Core/Platform.h',
  'Include/RmlUi/Core/Plugin.h',
  'Include/RmlUi/Core/Profiling.h',
  'Include/RmlUi/Core/PropertiesIteratorView.h',
  'Include/RmlUi/Core/Property.h',
  'Include/RmlUi/Core/PropertyDefinition.h',
  'Include/RmlUi/Core/PropertyDictionary.h',
  'Include/RmlUi/Core/PropertyIdSet.h',
  'Include/RmlUi/Core/PropertyParser.h',
  'Include/RmlUi/Core/PropertySpecification.h',
  'Include/RmlUi/Core/Rectangle.h',
  'Include/RmlUi/Core/RenderBox.h',
  'Include/RmlUi/Core/RenderInterface.h',
  'Include/RmlUi/Core/RenderInterfaceCompatibility.h',
  'Include/RmlUi/Core/RenderManager.h',
  'Include/RmlUi/Core/ScriptInterface.h',
  'Include/RmlUi/Core/ScrollTypes.h',
  'Include/RmlUi/Core/Span.h',
  'Include/RmlUi/Core/Spritesheet.h',
  'Include/RmlUi/Core/StableVector.h',
  'Include/RmlUi/Core/Stream.h',
  'Include/RmlUi/Core/StreamMemory.h',
  'Include/RmlUi/Core/StringUtilities.h',
  'Include/RmlUi/Core/StyleSheet.h',
  'Include/RmlUi/Core/StyleSheetContainer.h',
  'Include/RmlUi/Core/StyleSheetSpecification.h',
  'Include/RmlUi/Core/StyleSheetTypes.h',
  'Include/RmlUi/Core/StyleTypes.h',
  'Include/RmlUi/Core/SystemInterface.h',
  'Include/RmlUi/Core/TextInputContext.h',
  'Include/RmlUi/Core/TextInputHandler.h',
  'Include/RmlUi/Core/TextShapingContext.h',
  'Include/RmlUi/Core/Texture.h',
  'Include/RmlUi/Core/Traits.h',
  'Include/RmlUi/Core/Transform.h',
  'Include/RmlUi/Core/TransformPrimitive.h',
  'Include/RmlUi/Core/Tween.h',
  'Include/RmlUi/Core/TypeConverter.h',
  'Include/RmlUi/Core/Types.h',
  'Include/RmlUi/Core/URL.h',
  'Include/RmlUi/Core/UniqueRenderResource.h',
  'Include/RmlUi/Core/Unit.h',
  'Include/RmlUi/Core/Utilities.h',
  'Include/RmlUi/Core/Variant.h',
  'Include/RmlUi/Core/Vector2.h',
  'Include/RmlUi/Core/Vector3.h',
  'Include/RmlUi/Core/Vector4.h',
  'Include/RmlUi/Core/Vertex.h',
  'Include/RmlUi/Core/XMLNodeHandler.h',
  'Include/RmlUi/Core/XMLParser.h',
  'Include/RmlUi/Debugger.h',
  'Include/RmlUi/Debugger/Debugger.h',
  'Include/RmlUi/Debugger/Header.h',
  'Include/RmlUi/Lottie/ElementLottie.h',
  'Include/RmlUi/Lua.h',
  'Include/RmlUi/Lua/Header.h',
  'Include/RmlUi/Lua/IncludeLua.h',
  'Include/RmlUi/Lua/Interpreter.h',
  'Include/RmlUi/Lua/Lua.h',
  'Include/RmlUi/Lua/LuaType.h',
  'Include/RmlUi/Lua/Utilities.h',
  'Include/RmlUi/SVG/ElementSVG.h',
  subdir: 'RmlUi'
)

# Make headers available to the parent project
rmlui_dep = declare_dependency(
  include_directories: inc_dirs,
  link_with: rmlui_core,
)
