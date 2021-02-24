# Custom VFX RenderQueue

com.unity.visualeffectgraph@6.9.2-preview\Editor\Models\Contexts\Implementations\
VFXAbstractParticleOutput.cs

70.增加
[VFXSetting, SerializeField, Tooltip("Render Queue"), Header("Render Queue")]
        protected string customRenderQueue = null;

350.替换
shaderTags.Write(string.Format("Tags {{ \"Queue\"=\"{0}\" \"IgnoreProjector\"=\"{1}\" \"RenderType\"=\"{2}\" }}", String.IsNullOrEmpty(customRenderQueue) ? renderQueueStr : customRenderQueue, !isBlendModeOpaque, renderTypeStr));



# Custom CameraColorTexture queue

com.unity.render-pipelines.universal@7.4.3\Runtime\ForwardRenderer.cs

81. m_CopyColorPass = new CopyColorPass(RenderPassEvent.BeforeRenderingTransparents, m_SamplingMaterial);

    替换为

​            m_CopyColorPass = new CopyColorPass(RenderPassEvent.AfterRenderingTransparents, m_SamplingMaterial);